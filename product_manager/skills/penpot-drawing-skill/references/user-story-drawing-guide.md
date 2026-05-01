# User Story Drawing Guide

本指引提供在 Penpot 中绘制用户故事的绘制代码。

## 容器结构

```
所有用户故事 (Board, flex column)
└── 用户故事: XXX (Board, flex column)
    ├── 标题文本 (Text)
    ├── 描述文本 (Text)
    ├── 业务流程图 (Board, flex column)
    │   ├── 标题 (Text)
    │   └── 横向流程绘制区 (Board, flex row)
    │       ├── 步骤节点-矩形 (Board)
    │       ├── 水平箭头 (Rectangle)
    │       ├── 判断节点-菱形 (Board)
    │       ├── 水平箭头 (Rectangle)
    │       ├── 分支路径 (Board, flex row)
    │       │   ├── 是分支 (Board, flex column)
    │       │   │   ├── 分支箭头 (Rectangle)
    │       │   │   └── 步骤节点 (Board)
    │       │   └── 否分支 (Board, flex column)
    │       │       ├── 分支箭头 (Rectangle)
    │       │       └── 步骤节点 (Board)
    │       └── ...
    └── 页面地图 (Board, flex column)
        ├── 标题 (Text)
        └── 横向页面流 (Board, flex row)
            ├── 页面方框 (Board)
            │   ├── 页面名称 (Text)
            │   └── 点击元素 (Text)
            ├── 跳转箭头 (Board)
            │   ├── 箭头线 (Rectangle)
            │   └── 操作说明 (Text)
            ├── 页面方框 (Board)
            └── 返回路径 (Board)
                ├── 返回箭头 (Rectangle)
                └── 返回说明 (Text)
```

## 绘制规范

> 业务流程图绘制需同时遵循 `product_manager/skills/business-flow-chart/SKILL.md` 的规范：
> - 判断节点（菱形）必须有至少 2 个分支
> - 所有分支必须闭环，不允许悬空分支
> - 流程方向严格从左到右

---

## 4.4.1 父容器（所有用户故事）

```javascript
var parentBoard = penpot.createBoard();
parentBoard.name = "所有用户故事";
parentBoard.resize(1400, 2400);
parentBoard.fills = [{ fillColor: "#FAFAFA", fillOpacity: 1 }];
var parentFlex = penpotUtils.addFlexLayout(parentBoard, "column");
parentFlex.rowGap = 30;
parentFlex.topPadding = 50;
parentFlex.leftPadding = 50;
parentFlex.rightPadding = 50;
```

---

## 4.4.2 用户故事容器

```javascript
var storyBoard = penpot.createBoard();
storyBoard.name = "用户故事: " + storyName;
storyBoard.resize(1300, 700);
storyBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
storyBoard.borderRadius = 8;
storyBoard.shadows = [{
  shadowColor: "#000000", shadowOpacity: 0.1,
  shadowOffsetX: 0, shadowOffsetY: 2, shadowBlur: 8
}];
var storyFlex = penpotUtils.addFlexLayout(storyBoard, "column");
storyFlex.rowGap = 20;
storyFlex.topPadding = 20;
storyFlex.leftPadding = 20;
storyFlex.rightPadding = 20;
storyFlex.bottomPadding = 20;
```

---

## 4.4.3 业务流程图绘制（横向布局，UML活动图规范）

> **核心关注点**：业务流程图专注于**业务步骤和判断逻辑**，横向展示流程走向。

**业务流程图容器：**

```javascript
var bizFlowBoard = penpot.createBoard();
bizFlowBoard.name = "业务流程图";
bizFlowBoard.resize(1260, 280);
bizFlowBoard.fills = [{ fillColor: "#F8FAFC", fillOpacity: 1 }];
bizFlowBoard.borderRadius = 8;
bizFlowBoard.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var bizFlowFlex = penpotUtils.addFlexLayout(bizFlowBoard, "column");
bizFlowFlex.rowGap = 12;
bizFlowFlex.topPadding = 16;
bizFlowFlex.leftPadding = 20;
bizFlowFlex.rightPadding = 20;
bizFlowFlex.bottomPadding = 16;
```

**横向流程绘制区（横向排列节点和箭头）：**

```javascript
var flowArea = penpot.createBoard();
flowArea.name = "流程绘制区";
flowArea.resize(1220, 200);
flowArea.fills = [];
var flowAreaFlex = penpotUtils.addFlexLayout(flowArea, "row");
flowAreaFlex.columnGap = 12;
flowAreaFlex.alignItems = "center";
flowAreaFlex.topPadding = 10;
flowAreaFlex.bottomPadding = 10;
```

**步骤节点（矩形 - 普通业务步骤）：**

```javascript
var stepNode = penpot.createBoard();
stepNode.name = "步骤: " + stepName;
stepNode.resize(140, 64);
stepNode.fills = [{ fillColor: "#E3F2FD", fillOpacity: 1 }];
stepNode.strokes = [{ strokeColor: "#2196F3", strokeWidth: 1, strokeStyle: "solid" }];
stepNode.borderRadius = 4;

var stepText = penpot.createText(stepName);
stepText.fontSize = "13";
stepText.fills = [{ fillColor: "#1565C0", fillOpacity: 1 }];
stepText.growType = "auto-width";
stepNode.appendChild(stepText);
penpotUtils.setParentXY(stepText, 12, 22);

flowArea.appendChild(stepNode);
```

**判断节点（菱形 - 业务判断逻辑）：**

```javascript
var decisionNode = penpot.createBoard();
decisionNode.name = "判断: " + conditionText;
decisionNode.resize(140, 80);
decisionNode.fills = [{ fillColor: "#FFF3E0", fillOpacity: 1 }];
decisionNode.strokes = [{ strokeColor: "#FF9800", strokeWidth: 2, strokeStyle: "solid" }];
decisionNode.borderRadius = 8;

var decisionText = penpot.createText(conditionText);
decisionText.fontSize = "12";
decisionText.fills = [{ fillColor: "#E65100", fillOpacity: 1 }];
decisionText.growType = "auto-width";
decisionNode.appendChild(decisionText);
penpotUtils.setParentXY(decisionText, 12, 20);

var conditionLabel = penpot.createText("[" + conditionResult + "]");
conditionLabel.fontSize = "10";
conditionLabel.fills = [{ fillColor: "#FF9800", fillOpacity: 1 }];
conditionLabel.growType = "auto-width";
decisionNode.appendChild(conditionLabel);
penpotUtils.setParentXY(conditionLabel, 12, 46);

flowArea.appendChild(decisionNode);
```

**水平箭头（连接节点）：**

```javascript
var hArrow = penpot.createRectangle();
hArrow.name = "→";
hArrow.resize(24, 2);
hArrow.fills = [{ fillColor: "#90A4AE", fillOpacity: 1 }];

flowArea.appendChild(hArrow);
```

**分支路径（判断后的横向分支）：**

```javascript
// 分支容器（横向排列"是"和"否"路径）
var branchBoard = penpot.createBoard();
branchBoard.name = "分支: " + conditionText;
branchBoard.resize(380, 140);
branchBoard.fills = [];
var branchFlex = penpotUtils.addFlexLayout(branchBoard, "row");
branchFlex.columnGap = 24;
branchFlex.alignItems = "center";

// "是"分支（绿色）
var yesPath = penpot.createBoard();
yesPath.name = "是分支";
yesPath.resize(160, 120);
yesPath.fills = [];
var yesFlex = penpotUtils.addFlexLayout(yesPath, "column");
yesFlex.rowGap = 6;
yesFlex.alignItems = "center";

var yesLabel = penpot.createText("是");
yesLabel.fontSize = "11";
yesLabel.fills = [{ fillColor: "#4CAF50", fillOpacity: 1 }];
yesLabel.growType = "auto-width";
yesPath.appendChild(yesLabel);

var yesArrow = penpot.createRectangle();
yesArrow.resize(2, 16);
yesArrow.fills = [{ fillColor: "#4CAF50", fillOpacity: 1 }];
yesPath.appendChild(yesArrow);

var yesStep = penpot.createBoard();
yesStep.resize(140, 48);
yesStep.fills = [{ fillColor: "#E8F5E9", fillOpacity: 1 }];
yesStep.strokes = [{ strokeColor: "#4CAF50", strokeWidth: 1, strokeStyle: "solid" }];
yesStep.borderRadius = 4;
var yesStepText = penpot.createText(yesStepName);
yesStepText.fontSize = "12";
yesStepText.fills = [{ fillColor: "#2E7D32", fillOpacity: 1 }];
yesStepText.growType = "auto-width";
yesStep.appendChild(yesStepText);
penpotUtils.setParentXY(yesStepText, 10, 14);
yesPath.appendChild(yesStep);

branchBoard.appendChild(yesPath);

// "否"分支（红色）
var noPath = penpot.createBoard();
noPath.name = "否分支";
noPath.resize(160, 120);
noPath.fills = [];
var noFlex = penpotUtils.addFlexLayout(noPath, "column");
noFlex.rowGap = 6;
noFlex.alignItems = "center";

var noLabel = penpot.createText("否");
noLabel.fontSize = "11";
noLabel.fills = [{ fillColor: "#F44336", fillOpacity: 1 }];
noLabel.growType = "auto-width";
noPath.appendChild(noLabel);

var noArrow = penpot.createRectangle();
noArrow.resize(2, 16);
noArrow.fills = [{ fillColor: "#F44336", fillOpacity: 1 }];
noPath.appendChild(noArrow);

var noStep = penpot.createBoard();
noStep.resize(140, 48);
noStep.fills = [{ fillColor: "#FFEBEE", fillOpacity: 1 }];
noStep.strokes = [{ strokeColor: "#F44336", strokeWidth: 1, strokeStyle: "solid" }];
noStep.borderRadius = 4;
var noStepText = penpot.createText(noStepName);
noStepText.fontSize = "12";
noStepText.fills = [{ fillColor: "#C62828", fillOpacity: 1 }];
noStepText.growType = "auto-width";
noStep.appendChild(noStepText);
penpotUtils.setParentXY(noStepText, 10, 14);
noPath.appendChild(noStep);

branchBoard.appendChild(noPath);

flowArea.appendChild(branchBoard);
```

**合并节点（分支汇聚）：**

```javascript
var mergeNode = penpot.createBoard();
mergeNode.name = "汇聚";
mergeNode.resize(32, 32);
mergeNode.fills = [{ fillColor: "#ECEFF1", fillOpacity: 1 }];
mergeNode.strokes = [{ strokeColor: "#607D8B", strokeWidth: 1, strokeStyle: "solid" }];
mergeNode.borderRadius = 16; // 圆形表示汇聚

flowArea.appendChild(mergeNode);
```

---

## 4.4.4 页面地图绘制（横向页面流）

> **核心关注点**：页面地图展示**页面之间的跳转关系**，横向排列页面节点。

**页面地图容器：**

```javascript
var pageMapBoard = penpot.createBoard();
pageMapBoard.name = "页面地图";
pageMapBoard.resize(1260, 200);
pageMapBoard.fills = [{ fillColor: "#F8FAFC", fillOpacity: 1 }];
pageMapBoard.borderRadius = 8;
pageMapBoard.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var pageMapFlex = penpotUtils.addFlexLayout(pageMapBoard, "column");
pageMapFlex.rowGap = 12;
pageMapFlex.topPadding = 16;
pageMapFlex.leftPadding = 20;
pageMapFlex.rightPadding = 20;
pageMapFlex.bottomPadding = 16;
```

**横向页面流（横向排列页面和跳转箭头）：**

```javascript
var pageFlowArea = penpot.createBoard();
pageFlowArea.name = "页面流";
pageFlowArea.resize(1220, 140);
pageFlowArea.fills = [];
var pageFlowFlex = penpotUtils.addFlexLayout(pageFlowArea, "row");
pageFlowFlex.columnGap = 8;
pageFlowFlex.alignItems = "center";
pageFlowFlex.topPadding = 10;
pageFlowFlex.bottomPadding = 10;
```

**页面节点（方框代表页面）：**

```javascript
var pageNode = penpot.createBoard();
pageNode.name = "页面: " + pageName;
pageNode.resize(140, 90);
pageNode.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
pageNode.strokes = [{ strokeColor: "#3B82F6", strokeWidth: 2, strokeStyle: "solid" }];
pageNode.borderRadius = 6;
var pageNodeFlex = penpotUtils.addFlexLayout(pageNode, "column");
pageNodeFlex.rowGap = 4;
pageNodeFlex.topPadding = 10;
pageNodeFlex.leftPadding = 10;
pageNodeFlex.rightPadding = 10;
pageNodeFlex.bottomPadding = 10;
pageNodeFlex.alignItems = "center";

var pageNameText = penpot.createText(pageName);
pageNameText.fontSize = "13";
pageNameText.fontWeight = "600";
pageNameText.fills = [{ fillColor: "#1E40AF", fillOpacity: 1 }];
pageNameText.growType = "auto-width";
pageNode.appendChild(pageNameText);

var clickElementText = penpot.createText("点击: " + elementName);
clickElementText.fontSize = "10";
clickElementText.fills = [{ fillColor: "#64748B", fillOpacity: 1 }];
clickElementText.growType = "auto-width";
pageNode.appendChild(clickElementText);

pageFlowArea.appendChild(pageNode);
```

**跳转箭头（带操作说明）：**

```javascript
var jumpArrow = penpot.createBoard();
jumpArrow.name = "跳转: " + actionName;
jumpArrow.resize(70, 60);
jumpArrow.fills = [];
var jumpArrowFlex = penpotUtils.addFlexLayout(jumpArrow, "column");
jumpArrowFlex.rowGap = 2;
jumpArrowFlex.alignItems = "center";
jumpArrowFlex.justifyContent = "center";

var arrowLine = penpot.createRectangle();
arrowLine.resize(32, 2);
arrowLine.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
jumpArrow.appendChild(arrowLine);

var arrowText = penpot.createText(actionName);
arrowText.fontSize = "9";
arrowText.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
arrowText.growType = "auto-width";
jumpArrow.appendChild(arrowText);

pageFlowArea.appendChild(jumpArrow);
```

**返回路径（从后续页面返回）：**

```javascript
// 返回箭头（细线 + 返回文字）
var returnArrow = penpot.createBoard();
returnArrow.name = "返回路径";
returnArrow.resize(70, 40);
returnArrow.fills = [];
var returnArrowFlex = penpotUtils.addFlexLayout(returnArrow, "column");
returnArrowFlex.rowGap = 2;
returnArrowFlex.alignItems = "center";
returnArrowFlex.justifyContent = "center";

var returnLine = penpot.createRectangle();
returnLine.resize(32, 1);
returnLine.fills = [{ fillColor: "#9CA3AF", fillOpacity: 1 }];
returnArrow.appendChild(returnLine);

var returnText = penpot.createText("← 返回");
returnText.fontSize = "9";
returnText.fills = [{ fillColor: "#9CA3AF", fillOpacity: 1 }];
returnText.growType = "auto-width";
returnArrow.appendChild(returnText);

pageFlowArea.appendChild(returnArrow);
```

**多分支跳转（一个页面跳转到多个页面）：**

```javascript
// 分支跳转容器
var branchJump = penpot.createBoard();
branchJump.name = "分支跳转";
branchJump.resize(200, 100);
branchJump.fills = [];
var branchJumpFlex = penpotUtils.addFlexLayout(branchJump, "row");
branchJumpFlex.columnGap = 8;
branchJumpFlex.alignItems = "center";

// 主箭头
var mainArrow = penpot.createBoard();
mainArrow.resize(60, 60);
mainArrow.fills = [];
var mainArrowFlex = penpotUtils.addFlexLayout(mainArrow, "column");
mainArrowFlex.rowGap = 2;
mainArrowFlex.alignItems = "center";
mainArrowFlex.justifyContent = "center";

var mainArrowLine = penpot.createRectangle();
mainArrowLine.resize(24, 2);
mainArrowLine.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
mainArrow.appendChild(mainArrowLine);

var mainArrowText = penpot.createText("选择");
mainArrowText.fontSize = "9";
mainArrowText.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
mainArrowText.growType = "auto-width";
mainArrow.appendChild(mainArrowText);

branchJump.appendChild(mainArrow);

// 分支1
var branch1 = penpot.createBoard();
branch1.resize(120, 80);
branch1.fills = [];
var branch1Flex = penpotUtils.addFlexLayout(branch1, "column");
branch1Flex.rowGap = 4;
branch1Flex.alignItems = "center";

var branch1Arrow = penpot.createRectangle();
branch1Arrow.resize(2, 16);
branch1Arrow.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
branch1.appendChild(branch1Arrow);

var branch1Page = penpot.createBoard();
branch1Page.resize(100, 48);
branch1Page.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
branch1Page.strokes = [{ strokeColor: "#3B82F6", strokeWidth: 1, strokeStyle: "solid" }];
branch1Page.borderRadius = 4;
var branch1PageText = penpot.createText(branch1PageName);
branch1PageText.fontSize = "11";
branch1PageText.fills = [{ fillColor: "#1E40AF", fillOpacity: 1 }];
branch1PageText.growType = "auto-width";
branch1Page.appendChild(branch1PageText);
penpotUtils.setParentXY(branch1PageText, 8, 14);
branch1.appendChild(branch1Page);

branchJump.appendChild(branch1);

pageFlowArea.appendChild(branchJump);
```

---

## 4.5 防止重叠检查

绘制完成后，验证：
- [ ] 所有文本元素都是某个Board的子元素
- [ ] 所有包含文本的Board都有Flex布局
- [ ] 文本之间无重叠（间距 > 0）
