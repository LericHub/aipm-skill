---
name: 06-detail
description: 详细设计技能。基于用户故事绘制结果拆分用户故事，生成业务流程图、页面流程图，输出页面列表及页面结构和事件定义。
---

# 06 - Detail (详细设计)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容，作为本节点后续输出的参考基础
5. 优先加载「需求分析方法论」知识卡片，使用用户故事拆分相关方法指导后续工作

## 步骤2 职责
- 基于需求分析结果拆分用户故事
- 生成业务流程图和页面流程图
- 整合输出完整页面列表
- 定义页面结构和交互事件
- 事件列表细化
- 数据逻辑定义
- 埋点方案细化
- 页面维度的事件 + 异常输出
- 版本管理规范
- 文件保存规范

---

## 步骤3 加载指引

### 前序节点产出物

从 Memory.json 获取 drawUserStory 和 analysis 的产出：
- 调用 manage.read_memory() 获取 node_list
- 从 node_list 中获取 analysis 的 file 字段
- 读取 PRD 文件内容，获取业务对象、业务流程、页面流程等基础信息
- 可选：从 penpot.pages.userStory 获取 Penpot 用户故事页面参考

---

## 步骤4 核心原则：文件保存与版本管理（最重要）

**强制要求：所有产出物必须立即保存到文件，并严格进行版本管理！**

### 为什么重要？
1. **防丢失**：避免中断后内容丢失
2. **可追溯**：记录每个版本的完整内容
3. **版本对比**：方便用户对比不同版本
4. **流程衔接**：为后续节点提供稳定的输入源

---

## 步骤5 技能操作步骤

1. 读取 analysis 产出的文件内容
2. 基于analysis内容拆分用户故事（按工作流/规则/界面/数据维度拆分）
3. 根据用户故事生成业务流程图（Mermaid）和页面流程图
4. 基于用户故事和流程图整合输出完整页面列表
5. 逐页面定义页面结构和交互事件
6. 细化埋点方案和异常处理逻辑
7. **将生成的 Markdown 内容直接输出到用户消息中**（如果包含 Mermaid 代码块，需要特殊说明以便渲染）
8. 等待用户确认交付物

---

## 步骤6 输出模板结构

### 1. 业务背景和需求概述

内容来源：node4 (analysis) 的完整内容

### 2. 用户故事拆分（新增核心模块）

#### 2.1 Epic 级需求梳理
从analysis输出中提取核心业务域，确定Epic列表

#### 2.2 用户故事拆分
按照需求分析方法论中的用户故事拆分技巧（按工作流/规则/界面/数据维度），将Epic拆分为独立的用户故事，格式如下：

```
| 故事ID | 用户角色 | 用户故事描述 | 验收标准 | 优先级 |
|--------|----------|--------------|----------|--------|
| US-001 | 普通用户 | 作为一个用户，我想要查看我的积分余额，以便了解我可以兑换的商品 | 1. 登录后首页展示积分余额 2. 点击积分可查看积分明细 | Must have |
| US-002 | 普通用户 | 作为一个用户，我想要搜索商品，以便快速找到我想要兑换的商品 | 1. 首页有搜索入口 2. 支持关键词搜索 3. 搜索结果支持分页 | Should have |
```

### 3. 业务流程图和页面流程图

#### 3.1 业务流程图
基于用户故事梳理完整业务流程，使用Mermaid流程图展示：

```
graph TD
  A[用户登录] --> B[查看积分余额]
  B --> C[浏览/搜索商品]
  C --> D{商品符合需求?}
  D -->|是| E[兑换商品]
  D -->|否| C
  E --> F{积分足够?}
  F -->|是| G[确认订单]
  F -->|否| H[提示积分不足]
  G --> I[支付/积分扣除]
  I --> J[生成订单]
  J --> K[查看订单详情]
```

#### 3.2 页面流程图
基于用户故事和业务流程输出页面跳转路径：

```
首页 → 商品列表 → 商品详情 → 订单确认 → 订单支付 → 订单列表 → 订单详情
```

### 4. 页面列表整合

#### 4.1 C 端页面列表
```
| 页面ID | 页面名称 | 所属模块 | 功能描述 | 关联用户故事 |
|--------|----------|----------|----------|--------------|
| PAGE-C-001 | 首页 | 基础模块 | 展示用户信息、积分余额、快捷入口 | US-001 |
| PAGE-C-002 | 商品列表页 | 商品模块 | 展示商品列表、支持搜索和筛选 | US-002 |
| PAGE-C-003 | 商品详情页 | 商品模块 | 展示商品详情、兑换按钮 | US-003 |
```

#### 4.2 B 端页面列表
```
| 页面ID | 页面名称 | 所属模块 | 功能描述 | 关联用户故事 |
|--------|----------|----------|----------|--------------|
| PAGE-B-001 | 商品管理页 | 商品模块 | 商品上下架、编辑、库存管理 | US-010 |
| PAGE-B-002 | 订单管理页 | 订单模块 | 订单查询、发货、退款处理 | US-011 |
```

### 5. 页面结构和事件定义
**结构：每个页面一个第 3 层标题**

#### 5.1 {页面名称} (PAGE-ID)

##### 5.1.1 页面结构定义
```
- 顶部导航栏：返回按钮、页面标题、更多操作
- 内容区域：
  - 模块1：用户信息卡片（头像、昵称、积分）
  - 模块2：商品推荐列表（图片、名称、所需积分）
  - 模块3：功能入口（我的订单、我的收藏、设置）
- 底部Tab栏：首页、分类、购物车、我的
```

##### 5.1.2 事件列表
**事件编号规范：页面编号-feat-序号**

```
| 事件编号 | 事件名称 | 绑定页面 | 触发节点 | 事件描述 | 关联用户故事 |
|---------|---------|---------|---------|---------|--------------|
| /home/feat-01 | 查询会员信息 | 首页 | 载入时 | 用户登录后通过接口查询用户的积分和等级信息 | US-001 |
| /home/feat-02 | 刷新商品列表 | 首页 | 点击刷新 | 用户点击刷新按钮，重新加载商品列表 | US-002 |
| /home/feat-03 | 进入商品列表 | 首页 | 点击商品入口 | 用户点击商品入口，跳转到商品列表页 | US-002 |
```

##### 5.1.3 事件逻辑
针对每个事件，详细说明：
1. 业务流程图（Mermaid）
2. 页面跳转路径（若涉及跳转）
3. 接口交互逻辑（触发的接口、传输的数据、数据展示规则）
4. 异常场景和处理方案

**异常编号规范：事件编号-err序号**

```
| 异常场景 | 异常编号 | 判断方式 | 处理方案 |
|---------|---------|---------|---------|
| 未登录 | /home-feat-01-err01 | 检查 token 是否存在 | 显示登录按钮，引导用户登录 |
| 接口调用失败 | /home-feat-01-err02 | HTTP 状态码 != 200 | 显示"加载失败，请重试"提示 |
| 数据格式错误 | /home-feat-01-err03 | JSON 解析失败 | 显示默认的用户信息 |
```

### 6. 埋点方案
内容来源：node4 (analysis) 的埋点方案，或根据业务场景新增输出格式

---

## 步骤7 输出文件

### 7.1 文件追加逻辑

detail节点完成后，将内容追加到当前版本的PRD文档对应章节：

**PRD章节映射：**
- "详细设计"章节（## 3）：业务流程图、页面流程图、事件列表、异常场景
- "页面详细设计"章节（## 4）：按页面维度组织详细内容

### 7.2 输出路径

- 路径: `./{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md`
- 操作模式: 追加写入（不是覆盖）

---

## 步骤8 Penpot 绘制

在文档输出完成后，将 PRD 内容绘制到 Penpot 画布上。

### 8.1 加载 penpot-helper-skill

1. 读取 `penpot-helper-skill/SKILL.md`
2. 重点参考：
   - `references/mcp-generation-guide.md`（坐标系统、ES5 兼容）
   - `references/user-story-layout-guide.md`（Flex 布局防重叠）
   - `references/layout-system.md`（Flex/Grid 属性）

### 8.2 创建/获取 Penpot 页面

1. 检查 Memory.json 中 `penpot.pages.prd` 是否为空
2. 若为空：
   - 调用 `penpot.createPage()` 创建新页面
   - 页面命名：`PRD 详细设计 - V{version}`
   - 更新 Memory.json：`penpot.pages.prd = 新页面ID`
3. 若已存在：
   - 调用 `penpot.openPage()` 切换到该页面
   - 清除页面上的旧内容（从后往前遍历删除）

### 8.3 整体布局策略（横向多栏 + 按大标题分栏）

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

### 8.4 流程图绘制规范（矩形 + 菱形 + 箭头横向展示）

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

### 8.5 表格模拟规范（Rectangle + Text 组合）

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

### 8.6 内容映射规则

| PRD 章节 | 绘制方式 | 所在栏 |
|---------|---------|--------|
| 业务背景和需求概述 | 章节标题 + 段落文本 | 栏1 |
| 用户故事拆分 | 章节标题 + 模拟表格 | 栏2 |
| 业务流程图 | 章节标题 + 矩形+菱形+箭头流程图 | 栏3 |
| 页面流程图 | 章节标题 + 横向箭头流程 | 栏4 |
| 页面列表整合 | 章节标题 + 模拟表格 | 栏5 |
| 页面结构和事件定义 | 章节标题 + 嵌套子容器（每个页面一个） | 栏6 |
| 埋点方案 | 章节标题 + 段落文本 | 栏7 |

### 8.7 防止重叠策略

1. **根容器使用 Flex row**：所有栏横向排列，自动处理间距
2. **单栏使用 Flex column**：栏内内容纵向排列
3. **表格使用固定尺寸单元格**：确保表格不溢出
4. **流程图节点固定尺寸**：矩形 200×48，菱形 160×80
5. **嵌套深度控制**：最多 4 层 Board（根 → 栏 → 子容器 → 单元格/节点）
6. **绘制后验证**：
   - 检查所有元素都在父容器边界内
   - 检查栏与栏之间无重叠（columnGap > 0）
   - 检查表格单元格对齐正确

### 8.8 保存到 Memory.json

```json
{
  "penpot": {
    "pages": {
      "prd": "page-uuid"
    }
  }
}
```

### 8.9 用户通知

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

---

## 步骤9 完成后

1. 读取当前版本PRD文档：`./{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md`
2. 将detail节点产出追加到"详细设计"章节（## 3）
3. 将detail节点产出按页面维度追加到"页面详细设计"章节（## 4）
4. 保存更新后的PRD文档
5. 调用 manage.update_node_output("detail", "{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md")
6. 展示产出摘要
7. **执行步骤8 Penpot 绘制**
8. 等待用户确认
9. 确认后 → 调用 manage.update_node_status("detail", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 GenPrototype 节点
