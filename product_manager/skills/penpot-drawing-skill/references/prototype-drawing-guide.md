# Prototype Drawing Guide

本指引提供在 Penpot 中绘制页面原型设计稿的绘制代码。

## 容器结构

```
所有页面原型 (Board, flex column)
└── 页面: XXX (Board, flex column)
    ├── 页面标题 (Text)
    ├── 功能区域1 (Board)
    ├── 功能区域2 (Board)
    └── 操作按钮区 (Board, flex row)
```

---

## 4.3 绘制页面设计稿

### 4.3.1 父容器（所有页面原型）

```javascript
var parentBoard = penpot.createBoard();
parentBoard.name = "所有页面原型";
parentBoard.resize(1200, 3000);
parentBoard.fills = [{ fillColor: "#FAFAFA", fillOpacity: 1 }];
var parentFlex = penpotUtils.addFlexLayout(parentBoard, "column");
parentFlex.rowGap = 40;
parentFlex.topPadding = 50;
parentFlex.leftPadding = 50;
parentFlex.rightPadding = 50;
```

### 4.3.2 页面容器

```javascript
var pageBoard = penpot.createBoard();
pageBoard.name = "页面: " + pageName;
pageBoard.resize(375, 812); // 手机页面尺寸
pageBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
pageBoard.borderRadius = 12;
pageBoard.shadows = [{
  shadowColor: "#000000", shadowOpacity: 0.1,
  shadowOffsetX: 0, shadowOffsetY: 4, shadowBlur: 12
}];
var pageFlex = penpotUtils.addFlexLayout(pageBoard, "column");
pageFlex.rowGap = 16;
pageFlex.topPadding = 24;
pageFlex.leftPadding = 24;
pageFlex.rightPadding = 24;
pageFlex.bottomPadding = 24;
```

### 4.3.3 页面元素

**页面标题：**
```javascript
var pageTitle = penpot.createText(pageName);
pageTitle.fontSize = "20";
pageTitle.fontWeight = "600";
pageTitle.fills = [{ fillColor: "#1F2937", fillOpacity: 1 }];
pageTitle.growType = "auto-width";
pageBoard.appendChild(pageTitle);
```

**功能区域：**
```javascript
var functionArea = penpot.createBoard();
functionArea.name = "功能区域: " + areaName;
functionArea.resize(327, 120);
functionArea.fills = [{ fillColor: "#F3F4F6", fillOpacity: 1 }];
functionArea.borderRadius = 8;
var areaFlex = penpotUtils.addFlexLayout(functionArea, "column");
areaFlex.rowGap = 8;
areaFlex.topPadding = 12;
areaFlex.leftPadding = 12;
areaFlex.rightPadding = 12;
areaFlex.bottomPadding = 12;

var areaTitle = penpot.createText(areaName);
areaTitle.fontSize = "14";
areaTitle.fontWeight = "500";
areaTitle.fills = [{ fillColor: "#374151", fillOpacity: 1 }];
areaTitle.growType = "auto-width";
functionArea.appendChild(areaTitle);

var areaDesc = penpot.createText(areaDescription);
areaDesc.fontSize = "12";
areaDesc.fills = [{ fillColor: "#6B7280", fillOpacity: 1 }];
areaDesc.growType = "auto-height";
functionArea.appendChild(areaDesc);

pageBoard.appendChild(functionArea);
```

**操作按钮区：**
```javascript
var buttonArea = penpot.createBoard();
buttonArea.name = "操作按钮区";
buttonArea.resize(327, 48);
buttonArea.fills = [];
var buttonFlex = penpotUtils.addFlexLayout(buttonArea, "row");
buttonFlex.columnGap = 12;
buttonFlex.alignItems = "center";
buttonFlex.justifyContent = "end";

// 主按钮
var primaryBtn = penpot.createBoard();
primaryBtn.name = "按钮: " + buttonName;
primaryBtn.resize(100, 40);
primaryBtn.fills = [{ fillColor: "#3B82F6", fillOpacity: 1 }];
primaryBtn.borderRadius = 8;

var btnText = penpot.createText(buttonName);
btnText.fontSize = "14";
btnText.fontWeight = "500";
btnText.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
btnText.growType = "auto-width";
primaryBtn.appendChild(btnText);
penpotUtils.setParentXY(btnText, 20, 12);

buttonArea.appendChild(primaryBtn);
pageBoard.appendChild(buttonArea);
```

---

## 4.4 绘制内容参考

根据PRD中每个页面的详细设计：
- 页面名称 → 页面标题
- 功能描述 → 功能区域内容
- 事件列表 → 操作按钮区
- 异常场景 → 标注在对应功能区域旁
