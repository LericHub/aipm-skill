---
name: 07-genPrototype
description: 在 detail 节点产出确认后进入。在此节点基于PRD页面列表在Penpot中绘制页面设计稿，产出可交互的原型设计。
---

# 07 - GenPrototype (页面原型设计)

> **注意**：知识卡片加载已由 Router 统一处理。本节点接收 Router 传递的知识卡片内容作为输入。

## 步骤1 职责
- 读取最新版本PRD文档
- 解析页面列表（从"页面详细设计"章节提取）
- 在Penpot中创建/获取原型设计页面
- 绘制页面设计稿
- 通知用户查看Penpot页面

---

## 步骤3 加载指引

### 前序节点产出物

从 Memory.json 获取：
- detail节点的 `output.path`（PRD文档）
- penpot.fileId（Penpot文件ID）
- penpot.pages.prototype（原型页面ID，可能为空）

### 加载 penpot-drawing-skill

绘制时，加载 `product_manager/skills/penpot-drawing-skill/SKILL.md`，根据指引选择对应的绘制guide：
- 原型绘制 → `product_manager/skills/penpot-drawing-skill/references/prototype-drawing-guide.md`

---

## 步骤4 执行逻辑

### 4.1 读取PRD文档

1. 调用 manage.read_memory() 获取项目信息
2. 读取当前版本的PRD文件：`./{项目名}_{datetime}/output/{Version}/PRD_{Version}.md`
3. 从PRD文档的"页面详细设计"章节提取页面列表

### 4.2 连接 Penpot MCP 前的必要准备

> **⚠️ 关键步骤**：在连接 Penpot MCP 进行绘制前，必须先读取 `product_manager/skills/penpot-drawing-skill/SKILL.md` 中维护的统一 preflight 指引。

---

### 4.3 创建/获取Penpot页面

> **前提**：已读取 penpot-drawing-skill 指引。

1. 检查 Memory.json 中 `penpot.pages.prototype` 是否为空
2. 若为空：
   - **通过 MCP Plugin 创建新页面**：发送 `add-page` change，指定 `name` 为 `"prototype"`，`id` 为新生成的 UUID
   - 保存页面 ID：`penpot.pages.prototype = pageId`
   - 更新 Memory.json
3. 若已存在：
   - 通过MCP `execute_code` 切换到该页面
   - 清除页面上的旧内容（保留页面结构）

### 4.4 绘制页面设计稿

**容器结构和绘制代码**：请参考 `product_manager/skills/penpot-drawing-skill/references/prototype-drawing-guide.md`

该指引包含：
- 容器结构定义
- 父容器代码（所有页面原型 Board）
- 页面容器代码（375×812 手机尺寸）
- 页面元素绘制代码（标题、功能区域、操作按钮）
- 绘制内容参考（PRD 内容映射）

---

## 步骤5 输出

### 5.1 保存到Memory.json

```json
{
  "penpot": {
    "pages": {
      "prototype": "page-uuid"
    }
  }
}
```

### 5.2 用户通知

在对话中发送：
```
页面原型设计稿已绘制完成！

🎨 Penpot页面：{penpot页面URL}

包含以下页面设计：
- 页面1: {名称}
- 页面2: {名称}
- ...

请打开Penpot页面查看并确认。确认后将继续进入genHTML节点生成HTML原型。
```

---

## 步骤6 完成后

1. 读取当前版本PRD文档
2. 解析页面列表
3. 创建/获取Penpot原型设计页面
4. 绘制页面设计稿
   - 参考 `penpot-drawing-skill/references/prototype-drawing-guide.md`
5. 验证无重叠
6. 更新Memory.json的penpot.pages.prototype
7. 展示产出摘要（页面列表+Penpot链接）
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
   - **只有用户明确表达验收通过意图时，才能推进到 genHTML 节点**
   - **任何非验收意图（修改、补充、疑问、部分确认、犹豫）都必须停留在 genPrototype 节点，不得进入下一节点**
   - 用户表达"变更"、"加功能"、"改需求"等意图时，进入 change 节点

9. 等待用户确认
10. 确认后 → 调用 manage.update_node_status("genPrototype", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 genHTML 节点
