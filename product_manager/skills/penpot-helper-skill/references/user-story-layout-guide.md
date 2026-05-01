# User Story Layout Guide

Guide for drawing user-story diagrams in Penpot without overlapping elements.

## When to use this guide

- Drawing user stories after the ANALYSIS node
- Drawing page prototypes after the DETAIL node
- Any board that contains multiple text blocks stacked vertically

## The overlap problem

Penpot `shape.x` / `shape.y` are **page-absolute**, not parent-relative.  
If you create a text element and set `y = 20`, it lands at page y = 20, **not** 20 px inside its parent board.  
When the parent board itself is at y = 100, the text appears 80 px **above** the board, causing overlap with other elements.

## Prevention: always use Flex layout on the parent board

Give every container that holds text a **Flex layout** (`column` for vertical stacks, `row` for horizontal).  
Flex automatically positions children with gaps—no manual coordinates needed.

```javascript
// 1. Create the parent board
var storyBoard = penpot.createBoard();
storyBoard.name = "用户故事: 登录";
storyBoard.resize(1100, 500);
storyBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];

// 2. Attach a column flex layout
var flex = penpotUtils.addFlexLayout(storyBoard, "column");
flex.rowGap = 20;          // vertical gap between children
flex.topPadding = 20;
flex.leftPadding = 20;
flex.rightPadding = 20;
flex.bottomPadding = 20;

// 3. Create children and appendChild (no manual x/y needed)
var title = penpot.createText("用户故事: 登录");
title.fontSize = 18;
storyBoard.appendChild(title);   // flex places it automatically

var desc = penpot.createText("作为用户，我希望使用手机号登录");
desc.fontSize = 14;
storyBoard.appendChild(desc);    // placed below title with 20 px gap
```

## Nested containers

When a user story contains both a title/description and a flow-diagram area, nest a second board for the flows:

```javascript
// Flow container inside the story board
var flowContainer = penpot.createBoard();
flowContainer.name = "流程容器";
flowContainer.resize(1040, 380);
penpotUtils.addFlexLayout(flowContainer, "row");
flowContainer.flex.columnGap = 20;
storyBoard.appendChild(flowContainer);

// Business-flow board (column flex for its own text)
var bizFlow = penpot.createBoard();
bizFlow.name = "业务流程";
bizFlow.resize(500, 360);
bizFlow.fills = [{ fillColor: "#E3F2FD", fillOpacity: 1 }];
penpotUtils.addFlexLayout(bizFlow, "column");
bizFlow.flex.rowGap = 10;
bizFlow.flex.topPadding = 15;
bizFlow.flex.leftPadding = 15;
flowContainer.appendChild(bizFlow);

var bizTitle = penpot.createText("业务流程");
bizTitle.fontSize = 14;
bizFlow.appendChild(bizTitle);

var bizContent = penpot.createText("1. 输入手机号\n2. 获取验证码\n3. 验证登录");
bizContent.fontSize = 12;
bizFlow.appendChild(bizContent);
```

## Checklist after generation

Run these checks before declaring the layout done:

1. **No orphaned text** — every `Text` shape must be a child of a `Board` with flex.
2. **Gap verification** — `rowGap` / `columnGap` > 0 on every flex parent.
3. **Padding verification** — `topPadding` / `leftPadding` set so text does not touch board edges.
4. **Visual inspection** — open the Penpot page; text blocks should be clearly separated.

## Quick fix for existing overlap

If text already overlaps, wrap the affected elements in a new flex board instead of adjusting coordinates:

```javascript
// Instead of moving individual texts:
// text1.y = 100;  // fragile, breaks on resize
// text2.y = 150;

// Create a flex board and reparent:
var wrapper = penpot.createBoard();
penpotUtils.addFlexLayout(wrapper, "column");
wrapper.flex.rowGap = 10;
wrapper.appendChild(text1);
wrapper.appendChild(text2);
```
