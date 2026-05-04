# Detail Drawing Guide

本指引提供在 Penpot 中绘制 PRD 详细设计（Detail 节点独有产出）的绘制代码。

## 容器结构

```
Penpot Page
├── 栏1: 业务对象状态机 (Board, flex column, 固定宽度 480px)
│   ├── 章节标题
│   ├── 子容器: 状态定义表（模拟表格）
│   ├── 子容器: 状态流转表（模拟表格）
│   └── 子容器: 状态流转图（矩形+箭头横向流程）
└── 栏2: 页面结构和事件定义 (Board, flex column, 固定宽度 800px)
    ├── 章节标题
    ├── 子容器: 页面1（含结构/事件/逻辑）
    ├── 子容器: 页面2
    └── ...
```

---

## 8.3 整体布局

### 8.3.1 根容器（横向排列所有栏）

```javascript
var rootBoard = penpot.createBoard();
rootBoard.name = "PRD 详细设计 - V" + version;
rootBoard.resize(1400, 1600);
rootBoard.fills = [{ fillColor: "#F1F5F9", fillOpacity: 1 }];
var rootFlex = penpotUtils.addFlexLayout(rootBoard, "row");
rootFlex.columnGap = 40;
rootFlex.topPadding = 40;
rootFlex.leftPadding = 40;
rootFlex.rightPadding = 40;
rootFlex.bottomPadding = 40;
```

### 8.3.2 单栏容器

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

---

## 8.4 流程图绘制（矩形 + 菱形 + 箭头纵向展示）

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
flowFlex.alignItems = "center";

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

---

## 8.5 表格模拟（Rectangle + Text 组合）

### 8.5.1 表格容器

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

### 8.5.2 表头行

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

### 8.5.3 数据行

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

---

## 8.6 内容映射规则

| PRD 章节 | 绘制方式 | 所在栏 |
|---------|---------|--------|
| 业务对象状态机 | 章节标题 + 状态定义表 + 状态流转表 + 状态节点流程图 | 栏1 |
| 页面结构和事件定义 | 章节标题 + 嵌套子容器（每个页面一个） | 栏2 |

> 其他章节（业务背景、用户故事、业务流程图、页面列表、埋点方案）由对应节点绘制，Detail 节点只绘制其独有产出。

---

## 8.7 防止重叠策略

1. **根容器使用 Flex row**：所有栏横向排列，自动处理间距
2. **单栏使用 Flex column**：栏内内容纵向排列
3. **表格使用固定尺寸单元格**：确保表格不溢出
4. **流程图节点固定尺寸**：矩形 200×48，菱形 160×80
5. **嵌套深度控制**：最多 4 层 Board（根 → 栏 → 子容器 → 单元格/节点）
6. **绘制后验证**：
   - 检查所有元素都在父容器边界内
   - 检查栏与栏之间无重叠（columnGap > 0）
   - 检查表格单元格对齐正确
