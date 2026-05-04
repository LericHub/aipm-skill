---
name: penpot-prd-drawer
description: |
  将 PRD 详细设计内容绘制到 Penpot 画布的标准化流程。
  负责在 detail 节点完成后，把 PRD 的业务背景、用户故事、业务流程图、
  页面流程图、页面列表、页面结构事件定义、埋点方案等内容以横向多栏布局绘制到 Penpot。
  需在读取 penpot-helper-skill 指引后执行。
---

# Penpot PRD 绘制器 (penpot-prd-drawer)

## 触发时机

- detail 节点完成 PRD 文档输出后
- 需要将 PRD 内容可视化到 Penpot 画布时

## 前置依赖

执行本 skill 前，**必须先完成以下准备**：

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

4. **确认已理解以下关键规则后再继续**：
   - `shape.x` / `shape.y` 是页面绝对坐标，非父级相对坐标
   - 使用 `insertChild` 而非 `appendChild`（Flex 布局板除外）
   - 文本必须设置 `growType`（`auto-width` 或 `auto-height`）
   - 嵌套深度不超过 4 层 Board
   - 先构建容器树，再设置样式，最后填充内容

> API 与页面管理口径统一参考 `product_manager/skills/penpot-helper-skill/references/api-reference.md`。

## 步骤1 创建/获取 Penpot 页面

1. 检查 Memory.json 中 `penpot.pages.prd` 是否为空
2. 若为空：
   - **通过 MCP Plugin 创建新页面**：发送 `add-page` change，指定 `name` 为 `"PRD"`，`id` 为新生成的 UUID
   - 保存生成的 `pageId`
   - 更新 Memory.json：`penpot.pages.prd = pageId`
3. 若已存在：
   - 通过MCP `execute_code` 切换到该页面
   - 清除页面上的旧内容（从后往前遍历删除）

## 步骤2 整体布局策略（横向多栏 + 按大标题分栏）

**页面布局结构**：
```
Penpot Page
├── 栏1: 业务背景和需求概述 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   ├── 子容器: 需求背景
│   └── 子容器: 需求目标
├── 栏2: 用户故事拆分 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   ├── 子容器: Epic 列表
│   └── 子容器: 用户故事表格（模拟表格）
├── 栏3: 业务流程图 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   └── 子容器: 流程图绘制区（矩形+菱形+箭头）
├── 栏4: 页面流程图 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   └── 子容器: 页面跳转流程（横向箭头）
├── 栏5: 页面列表整合 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   ├── 子容器: C 端页面表格（模拟表格）
│   └── 子容器: B 端页面表格（模拟表格）
├── 栏6: 页面结构和事件定义 (Board, flex column, 固定宽度 420px)
│   ├── 章节标题
│   ├── 子容器: 页面1（含结构/事件/逻辑）
│   ├── 子容器: 页面2
│   └── ...
└── 栏7: 埋点方案 (Board, flex column, 固定宽度 420px)
    ├── 章节标题
    └── 子容器: 埋点内容
```

**根容器（横向排列所有栏）**：
```javascript
var rootBoard = penpot.createBoard();
rootBoard.name = "PRD 详细设计 - V" + version;
rootBoard.resize(3200, 1400);
rootBoard.fills = [{ fillColor: "#F1F5F9", fillOpacity: 1 }];
var rootFlex = penpotUtils.addFlexLayout(rootBoard, "row");
rootFlex.columnGap = 40;
rootFlex.topPadding = 40;
rootFlex.leftPadding = 40;
rootFlex.rightPadding = 40;
rootFlex.bottomPadding = 40;
```

**单栏容器**：
```javascript
var columnBoard = penpot.createBoard();
columnBoard.name = "栏: " + sectionTitle;
columnBoard.resize(420, 1200);
columnBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
columnBoard.borderRadius = 12;
columnBoard.shadows = [{
  shadowColor: "#000000", shadowOpacity: 0.08,
  shadowOffsetX: 0, shadowOffsetY: 2, shadowBlur: 8
}];
var colFlex = penpotUtils.addFlexLayout(columnBoard, "column");
colFlex.rowGap = 20;
colFlex.topPadding = 24;
colFlex.leftPadding = 20;
colFlex.rightPadding = 20;
colFlex.bottomPadding = 24;
```

## 步骤3 流程图绘制规范（矩形 + 菱形 + 箭头横向展示）

**业务流程图绘制**：
```javascript
// 流程图容器
var flowBoard = penpot.createBoard();
flowBoard.name = "业务流程图";
flowBoard.resize(380, 800);
flowBoard.fills = [{ fillColor: "#FAFAFA", fillOpacity: 1 }];
flowBoard.borderRadius = 8;
var flowFlex = penpotUtils.addFlexLayout(flowBoard, "column");
flowFlex.rowGap = 16;
flowFlex.topPadding = 16;
flowFlex.leftPadding = 16;
flowFlex.rightPadding = 16;
flowFlex.bottomPadding = 16;
flowFlex.alignItems = "center"; // 节点横向居中

// 流程节点（矩形 - 普通步骤）
var stepRect = penpot.createRectangle();
stepRect.name = "步骤: 用户登录";
stepRect.resize(200, 48);
stepRect.fills = [{ fillColor: "#E3F2FD", fillOpacity: 1 }];
stepRect.borderRadius = 8;
stepRect.strokes = [{ strokeColor: "#2196F3", strokeWidth: 1, strokeStyle: "solid" }];

// 流程节点文本
var stepText = penpot.createText("用户登录");
stepText.fontSize = "14";
stepText.fills = [{ fillColor: "#1565C0", fillOpacity: 1 }];
stepText.growType = "auto-width";

// 判断节点（菱形 - 条件判断，用橙色边框矩形表示）
var decisionRect = penpot.createRectangle();
decisionRect.name = "判断: 积分足够?";
decisionRect.resize(160, 80);
decisionRect.fills = [{ fillColor: "#FFF3E0", fillOpacity: 1 }];
decisionRect.borderRadius = 8;
decisionRect.strokes = [{ strokeColor: "#FF9800", strokeWidth: 2, strokeStyle: "solid" }];

// 箭头（用细长的 Rectangle 模拟垂直箭头）
var arrow = penpot.createRectangle();
arrow.name = "箭头";
arrow.resize(2, 24);
arrow.fills = [{ fillColor: "#90A4AE", fillOpacity: 1 }];
```

**流程图布局**：使用 Flex column 纵向排列，每个节点之间用箭头连接，节点横向居中对齐。

## 步骤4 表格模拟规范（Rectangle + Text 组合）

**表格容器**：
```javascript
var tableBoard = penpot.createBoard();
tableBoard.name = "表格: 用户故事";
tableBoard.resize(360, 300);
tableBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
tableBoard.borderRadius = 4;
tableBoard.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var tableFlex = penpotUtils.addFlexLayout(tableBoard, "column");
tableFlex.rowGap = 0;
```

**表头行**：
```javascript
var headerRow = penpot.createBoard();
headerRow.name = "表头行";
headerRow.resize(360, 36);
headerRow.fills = [{ fillColor: "#F1F5F9", fillOpacity: 1 }];
var headerFlex = penpotUtils.addFlexLayout(headerRow, "row");
headerFlex.columnGap = 0;

// 表头单元格
var headerCell1 = penpot.createBoard();
headerCell1.resize(80, 36);
headerCell1.fills = [{ fillColor: "#F1F5F9", fillOpacity: 1 }];
headerCell1.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var cellText1 = penpot.createText("故事ID");
cellText1.fontSize = "12";
cellText1.fontWeight = "600";
cellText1.fills = [{ fillColor: "#475569", fillOpacity: 1 }];
cellText1.growType = "auto-width";
headerCell1.appendChild(cellText1);
penpotUtils.setParentXY(cellText1, 8, 10);
headerRow.appendChild(headerCell1);
// ... 更多表头单元格
```

**数据行**：
```javascript
var dataRow = penpot.createBoard();
dataRow.name = "数据行";
dataRow.resize(360, 32);
dataRow.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
var dataFlex = penpotUtils.addFlexLayout(dataRow, "row");
dataFlex.columnGap = 0;

// 数据单元格
var dataCell1 = penpot.createBoard();
dataCell1.resize(80, 32);
dataCell1.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
dataCell1.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var dataText1 = penpot.createText("US-001");
dataText1.fontSize = "12";
dataText1.fills = [{ fillColor: "#64748B", fillOpacity: 1 }];
dataText1.growType = "auto-width";
dataCell1.appendChild(dataText1);
penpotUtils.setParentXY(dataText1, 8, 8);
dataRow.appendChild(dataCell1);
// ... 更多数据单元格
```

## 步骤5 内容映射规则

| PRD 章节 | 绘制方式 | 所在栏 |
|---------|---------|--------|
| 业务背景和需求概述 | 章节标题 + 段落文本 | 栏1 |
| 用户故事拆分 | 章节标题 + 模拟表格 | 栏2 |
| 业务流程图 | 章节标题 + 矩形+菱形+箭头流程图 | 栏3 |
| 页面流程图 | 章节标题 + 横向箭头流程 | 栏4 |
| 页面列表整合 | 章节标题 + 模拟表格 | 栏5 |
| 页面结构和事件定义 | 章节标题 + 嵌套子容器（每个页面一个） | 栏6 |
| 埋点方案 | 章节标题 + 段落文本 | 栏7 |

## 步骤6 防止重叠策略

1. **根容器使用 Flex row**：所有栏横向排列，自动处理间距
2. **单栏使用 Flex column**：栏内内容纵向排列
3. **表格使用固定尺寸单元格**：确保表格不溢出
4. **流程图节点固定尺寸**：矩形 200×48，菱形 160×80
5. **嵌套深度控制**：最多 4 层 Board（根 → 栏 → 子容器 → 单元格/节点）
6. **绘制后验证**：
   - 检查所有元素都在父容器边界内
   - 检查栏与栏之间无重叠（columnGap > 0）
   - 检查表格单元格对齐正确

## 步骤7 保存到 Memory.json

```json
{
  "penpot": {
    "pages": {
      "prd": "page-uuid"
    }
  }
}
```

## 步骤8 用户通知

在对话中发送：
```
PRD 详细设计已绘制完成！

📋 Penpot页面：{penpot页面URL}

包含以下栏目：
- 栏1: 业务背景和需求概述
- 栏2: 用户故事拆分
- 栏3: 业务流程图
- 栏4: 页面流程图
- 栏5: 页面列表整合
- 栏6: 页面结构和事件定义
- 栏7: 埋点方案

请打开Penpot页面查看并确认。确认后将继续进入GenPrototype节点。
```
