---
name: penpot-drawing
description: Penpot 绘制指引。当需要在 Penpot 中绘制用户故事、页面原型等设计稿时触发此 skill。
---

# Penpot Drawing Skill

当需要在 Penpot 中绘制用户故事或页面原型时，使用此 skill 获取对应的绘制指引。

## 触发条件

- 执行 drawUserStory 节点时
- 执行 genPrototype 节点时
- 需要在 Penpot 中绘制用户故事或页面原型时

## 前置条件

在开始绘制前，必须先加载以下文件：

1. `product_manager/skills/penpot-helper-skill/SKILL.md` - Penpot 通用规则
2. `product_manager/skills/penpot-helper-skill/references/mcp-generation-guide.md` - 坐标系统、ES5 兼容
3. `product_manager/skills/penpot-helper-skill/references/user-story-layout-guide.md` - Flex 布局防重叠
4. `product_manager/skills/penpot-helper-skill/references/layout-system.md` - Flex/Grid 属性

## 关键规则（来自 penpot-helper-skill）

在开始绘制前，确认已理解以下关键规则：

- ✅ `shape.x` / `shape.y` 是页面绝对坐标，非父级相对坐标
- ✅ 使用 `insertChild` 而非 `appendChild`（Flex 布局板除外）
- ✅ 文本必须设置 `growType`（`auto-width` 或 `auto-height`）
- ✅ 嵌套深度不超过 4 层 Board
- ✅ 先构建容器树，再设置样式，最后填充内容

## 指引索引

根据绘制场景选择对应的指引：

| 场景 | 指引文件 |
|-----|---------|
| 用户故事绘制（drawUserStory 节点） | `references/user-story-drawing-guide.md` |
| 原型绘制（genPrototype 节点） | `references/prototype-drawing-guide.md` |
