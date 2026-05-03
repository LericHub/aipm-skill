---
name: 05-drawUserStory
description: 在 analysis 节点产出确认后进入。在此节点将PRD中的用户故事可视化绘制到Penpot画布，产出用户故事图。
-----·

# 05 - DrawUserStory (用户故事绘制)

> **注意**：知识卡片加载已由 Router 统一处理。本节点接收 Router 传递的知识卡片内容作为输入。

## 步骤1 职责
- 在Penpot中创建/获取项目文件
- 读取analysis节点产出的PRD文档
- 解析用户故事列表
- 在Penpot中创建/获取用户故事页面
- 绘制用户故事容器结构
- 通知用户查看Penpot页面

---

## 步骤3 加载指引

### 前序节点产出物

从 Memory.json 获取：
- analysis节点的 `output.path`（PRD文档）
- penpot.fileId（Penpot文件ID，可能为空）
- penpot.pages.userStory（用户故事页面ID，可能为空）

### 加载 penpot-drawing-skill

绘制前只需读取 `product_manager/skills/penpot-drawing-skill/SKILL.md`：
- 其中已统一维护 Penpot preflight、helper 依赖、MCP 关键规则和布局优化入口
- 用户故事绘制指引 → `product_manager/skills/penpot-drawing-skill/references/user-story-drawing-guide.md`

### 加载 penpot-api配置

读取 `product_manager/references/penpot-api.yaml`，获取API端点配置：
- base_url: `https://design.penpot.app/api/rpc`
- auth: `Authorization: Token ${PENPOT_ACCESS_TOKEN}`
- endpoints: create_file, add_page 等

---

## 步骤4 执行逻辑

### 4.1 创建/获取Penpot文件

> **目标**：通过Penpot API自动创建项目文件。成功则记录URL进入下一步；失败则引导用户手动创建。

1. 检查 Memory.json 中 `penpot.fileId` 是否为空
2. **若为空**（首次进入drawUserStory节点）：
   - **调用API创建新文件**：
     ```
     POST https://design.penpot.app/api/rpc/command/create-file
     Authorization: Token <access_token>
     Content-Type: application/json

     {
       "name": "{projectName}_{datetime}",
       "projectId": null
     }
     ```
   - **成功响应**：
     ```json
     {
       "id": "uuid",
       "name": "string",
       "projectId": "uuid"
     }
     ```
     - 保存文件信息：
       - `penpot.fileId = response.id`
       - `penpot.projectUrl = https://design.penpot.app/#/workspace?file-id={response.id}`
       - 更新 Memory.json
     - 向用户发送链接：
       ```
       📎 Penpot文件已创建：{penpot.projectUrl}

       请在浏览器中打开上方链接，确认Penpot文件可正常访问。
       确认后请回复"已打开"，我将开始绘制用户故事。
       ```
     - **暂停执行，等待用户确认**
   - **失败响应**：
     ```json
     {
       "type": "error",
       "code": "PENPOT-E0001",
       "message": "Authentication failed",
       "detail": "..."
     }
     ```
     - 向用户展示："❌ Penpot 文件创建失败：{error.message}"
     - 进入**手动创建引导流程**（见 4.1.1）
3. **若已存在**（用户已确认过文件）：
   - 继续下一步

#### 4.1.1 手动创建引导流程

当API创建失败时，引导用户手动创建：

1. **向用户发送引导信息**：
   ```
   由于自动创建失败，请手动创建Penpot文件：

   1. 打开 Penpot：https://design.penpot.app
   2. 创建一个新项目（Project）
   3. 在该项目下创建一个文件（File），命名为：{projectName}_{datetime}
   4. 将文件URL发送给我

   💡 提示：确保使用与当前账号一致的个人团队
   ```
2. **接收用户发送的文件URL**
3. **解析URL**：
   ```javascript
   var urlObj = new URL(userInputUrl);
   var teamId = urlObj.searchParams.get("team-id");
   var fileId = urlObj.searchParams.get("file-id");
   ```
4. **校验团队一致性**：
   - 获取当前用户所属团队ID（通过 `GET /api/rpc/query/get-profile`）
   - 若 `teamId` 与当前用户团队不一致：
     - 提示用户："⚠️ 检测到团队不一致。请使用个人团队创建文件，或联系管理员添加权限。"
     - 重新进入手动创建引导
   - 若一致：
     - 保存 `penpot.fileId = fileId`
     - 保存 `penpot.projectUrl = userInputUrl`
     - 更新 Memory.json
     - 继续下一步

---

### 4.2 读取PRD文档

1. 调用 manage.read_memory() 获取项目信息
2. 读取analysis节点的PRD文件：`./{项目名}_{datetime}/output/{Version}/PRD_{Version}.md`
3. 从PRD文档的"需求分析"章节提取用户故事列表

---

### 4.3 连接 Penpot MCP 前的必要准备

> **⚠️ 关键步骤**：在连接 Penpot MCP 进行绘制前，必须先读取 `product_manager/skills/penpot-drawing-skill/SKILL.md` 中维护的统一 preflight 指引。

---

### 4.4 创建/获取Penpot页面（用户确认后）

> **前提**：用户已确认在浏览器中打开Penpot文件链接，且已完成 `penpot-drawing-skill` preflight。
>
> **优化说明**：Penpot 创建文件时会自动创建一个默认页面（通常名为 "Page 1"）。为避免重复创建页面，直接重命名该默认页面为 "用户故事"，而非新建页面。

1. 检查 Memory.json 中 `penpot.pages.userStory` 是否为空
2. **若为空**（首次进入 drawUserStory 节点）：
   - **获取文件页面列表**：
     ```
     POST https://design.penpot.app/api/rpc/query/list-pages
     Authorization: Token <access_token>
     Content-Type: application/json

     {
       "fileId": "{penpot.fileId}"
     }
     ```
   - **响应示例**：
     ```json
     {
       "pages": [
         {
           "id": "page-uuid",
           "name": "Page 1"
         }
       ]
     }
     ```
   - **重命名默认页面为"用户故事"**：
     ```
     POST https://design.penpot.app/api/rpc/command/rename-page
     Authorization: Token <access_token>
     Content-Type: application/json

     {
       "fileId": "{penpot.fileId}",
       "pageId": "{response.pages[0].id}",
       "name": "用户故事"
     }
     ```
   - 保存页面ID：`penpot.pages.userStory = response.pages[0].id`
   - 更新 Memory.json
3. **若已存在**（非首次进入）：
   - 通过 MCP `execute_code` 切换到该页面
   - **清除旧内容**（从后往前遍历删除）：
     ```javascript
     var root = penpot.root;
     var kids = root.children.slice();
     for (var i = kids.length - 1; i >= 0; i--) {
       kids[i].remove();
     }
     ```
4. ⚠️ **如果获取/重命名/打开失败**：
   - 记录错误原因
   - 向用户展示错误信息："❌ Penpot 页面操作失败：{错误原因}"
   - **停止执行，不进入下一步**
   - 提供重试指引，等待用户处理后再重试

---

### 4.5 绘制用户故事容器

⚠️ **错误处理**：在绘制过程中，任何 Penpot API 调用失败都应：
- 记录错误原因
- 向用户展示错误信息："❌ Penpot 绘制失败：{错误原因}"
- **停止执行，不进入下一步**
- 提供重试指引，等待用户处理后再重试

**容器结构和绘制代码**：请参考 `product_manager/skills/penpot-drawing-skill/references/user-story-drawing-guide.md`

该指引包含：
- 容器结构定义
- 父容器代码（所有用户故事 Board）
- 用户故事容器代码
- 业务流程图绘制代码（判断节点、分支路径、水平箭头、汇聚节点）
- 页面地图绘制代码（页面节点、跳转箭头、返回路径、多分支跳转）
- 防止重叠检查清单

**绘制规范（参考 penpot-helper-skill 和 business-flow-chart）**：

> 业务流程图绘制需同时遵循 `product_manager/skills/business-flow-chart/SKILL.md` 的规范：
> - 判断节点（菱形）必须有至少 2 个分支
> - 所有分支必须闭环，不允许悬空分支
> - 流程方向严格从左到右

---

## 步骤5 输出

### 5.1 保存到Memory.json

```json
{
  "penpot": {
    "fileId": "file-uuid",
    "projectUrl": "https://design.penpot.app/#/workspace?file-id=...",
    "pages": {
      "userStory": "page-uuid"
    }
  }
}
```

### 5.2 用户通知

在对话中发送：
```
用户故事已绘制完成！

📋 Penpot页面：{penpot页面URL}

包含以下用户故事：
- 用户故事1: {名称}
- 用户故事2: {名称}
- ...

请打开Penpot页面查看并确认。确认后将继续进入Detail节点。
```

---

## 步骤6 完成后

### 6.1 成功路径

1. **创建/获取Penpot文件（步骤4.1）**：
   - 若 `penpot.fileId` 为空，调用API创建文件
   - 成功：记录 fileId 和 projectUrl
   - 失败：展示错误，进入手动创建引导
   - 向用户发送Penpot文件链接
   - **等待用户确认已打开链接**
2. **读取PRD文档（步骤4.2）**：
   - 解析用户故事列表
3. **创建/获取Penpot页面（步骤4.3，用户确认后执行）**：
   - 调用API创建页面或打开已有页面
   - 清除旧内容（如有）
4. **绘制用户故事容器（步骤4.4）**
   - 参考 `penpot-drawing-skill/references/user-story-drawing-guide.md`
5. **验证无重叠（步骤4.5）**
6. 更新Memory.json的penpot.pages.userStory
7. 展示产出摘要：
   ```
   用户故事已绘制完成！

   📋 Penpot页面：{penpot页面URL}

   包含以下用户故事：
   - 用户故事1: {名称}
   - 用户故事2: {名称}
   - ...

   请打开Penpot页面查看并确认。确认后将继续进入Detail节点。
   ```
8. **用户验收标准**

   产出展示后，明确告知用户以下验收规则：

   **验收通过意图（可进入下一节点）：**
   - 明确确认："确认"、"没问题"、"可以"、"好的"、"通过"、"验收通过"
   - 推进指令："继续"、"下一步"、"往下走"、"进入下一阶段"
   - 满意表达："满意"、"就这样"、"定稿"、"OK"、"ok"

   **非验收意图（停留在当前节点）：**
   - 修改请求："修改"、"不对"、"重来"、"重新生成"、"调整"、"改一下" → 重新执行当前节点
   - 补充需求："补充"、"还有"、"另外"、"加上"、"漏了" → 补充内容后重新展示
   - 疑问咨询："为什么"、"什么意思"、"不懂"、"解释一下" → 解答疑问后继续等待确认
   - 部分确认："这部分可以"、"其他的再改改" → 视为未完全确认，继续修改
   - 拖延/犹豫："再看看"、"先这样"、"待定" → 明确询问用户是否确认

   **重要规则：**
   - **只有用户明确表达验收通过意图时，才能推进到 detail 节点**
   - **任何非验收意图（修改、补充、疑问、部分确认、犹豫）都必须停留在 drawUserStory 节点，不得进入下一节点**
   - 用户表达"变更"、"加功能"、"改需求"等意图时，进入 change 节点

9. 等待用户确认
10. 确认后 → 调用 manage.update_node_status("drawUserStory", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 Detail 节点

### 6.2 失败路径

如果在步骤4.1、4.3或4.4中 Penpot 操作失败：

1. **停止执行**，不更新 Memory.json
2. 向用户展示错误信息：
   ```
   ❌ Penpot 操作失败

   失败原因：{具体错误原因}

   请检查以下事项后重试：
   - Penpot 服务是否正常访问
   - 网络连接是否稳定
   - Penpot 文件权限是否正确

   您可以：
   1. 说"重试" — 我将重新尝试 Penpot 操作
   2. 说"跳过" — 跳过 Penpot 绘制，直接进入 Detail 节点（Penpot 内容可后续补充）
   ```
3. 等待用户指示
   - 用户说"重试" → 重新执行失败步骤
   - 用户说"跳过" → 直接进入 Detail 节点（不更新 penpot.pages.userStory）
