---
name: 07-genPrototype
description: 页面原型设计节点。读取PRD文档中的页面列表，在Penpot画布中绘制页面设计稿。
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
- detail节点的file路径（PRD文档）
- penpot.fileId（Penpot文件ID）
- penpot.pages.prototype（原型页面ID，可能为空）

### 加载 penpot-drawing-skill

绘制时，加载 `product_manager/skills/penpot-drawing-skill/SKILL.md`，根据指引选择对应的绘制guide：
- 原型绘制 → `product_manager/skills/penpot-drawing-skill/references/prototype-drawing-guide.md`

---

## 步骤4 执行逻辑

### 4.1 读取PRD文档

1. 调用 manage.read_memory() 获取项目信息
2. 读取当前版本的PRD文件：`./{项目名}_{datetime}/output/V{version}/PRD_{version}.md`
3. 从PRD文档的"页面详细设计"章节提取页面列表

### 4.2 连接 Penpot MCP 前的必要准备

> **⚠️ 关键步骤**：在连接 Penpot MCP 进行绘制前，**必须先读取 penpot-helper-skill 的指引文件**。

1. **读取 penpot-helper-skill 主文件**：
   - 读取 `product_manager/skills/penpot-helper-skill/SKILL.md`
   - 重点理解「Core mental model」和「Preventing overlap」章节

2. **读取关键参考文件**：
   - 读取 `product_manager/skills/penpot-helper-skill/references/mcp-generation-guide.md`
     - 理解坐标系统（绝对坐标 vs 相对坐标）
     - 掌握 ES5 兼容性要求
     - 熟悉分步生成工作流
   - 读取 `product_manager/skills/penpot-helper-skill/references/user-story-layout-guide.md`
     - 掌握 Flex 布局防重叠技巧
   - 读取 `product_manager/skills/penpot-helper-skill/references/layout-system.md`
     - 理解 Flex/Grid 属性映射

3. **布局优化（如需要）**：
   - 若绘制后页面存在过多空白，读取 `product_manager/skills/penpot-layout-optimizer/SKILL.md`
   - 应用内容驱动尺寸策略，消除过度空白

3. **确认已理解以下关键规则后再继续**：
   - ✅ `shape.x` / `shape.y` 是页面绝对坐标，非父级相对坐标
   - ✅ 使用 `insertChild` 而非 `appendChild`（Flex 布局板除外）
   - ✅ 文本必须设置 `growType`（`auto-width` 或 `auto-height`）
   - ✅ 嵌套深度不超过 4 层 Board
   - ✅ 先构建容器树，再设置样式，最后填充内容

---

### 4.3 创建/获取Penpot页面

> **前提**：已读取 penpot-drawing-skill 指引。

1. 检查 Memory.json 中 `penpot.pages.prototype` 是否为空
2. 若为空：
   - **调用API创建新页面**：
     ```
     POST https://design.penpot.app/api/rpc/command/add-page
     Authorization: Token ${PENPOT_ACCESS_TOKEN}
     Content-Type: application/json

     {
       "fileId": "{penpot.fileId}",
       "name": "prototype"
     }
     ```
   - 保存响应中的 `pageId`
   - 更新 Memory.json：`penpot.pages.prototype = pageId`
3. 若已存在：
   - 通过MCP `execute_code` 切换到该页面
   - 清除页面上的旧内容（保留页面结构）

### 4.3 绘制页面设计稿

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
8. 等待用户确认
9. 确认后 → 调用 manage.update_node_status("genPrototype", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 genHTML 节点
