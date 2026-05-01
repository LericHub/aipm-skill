---
name: 06-detail
description: 详细设计技能。基于 Analysis 节点产出的用户故事和业务流程，生成业务对象状态机、页面结构和事件定义（含异常处理、埋点触发时机）。
---

# 06 - Detail (详细设计)

> **注意**：知识卡片加载已由 Router 统一处理。本节点接收 Router 传递的知识卡片内容作为输入。

## 步骤1 职责
- 读取并整合 Analysis 节点产出的用户故事和业务流程
- 生成业务对象状态机（如有复杂状态机业务对象）
- 定义页面结构和交互事件
- 细化事件列表（关联用户故事）
- 定义数据逻辑（前置条件、数据血缘、目标状态、数据契约）
- 细化异常处理和埋点触发时机
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

1. 读取 Analysis 节点产出的 PRD 文件内容
2. 引用 Analysis 节点的用户故事和业务流程
3. 检测是否存在具有复杂状态机的业务对象：
   - 解析 PRD 文档 `2.4 业务对象` 章节
   - 检查业务对象属性中是否包含"状态"相关字段（状态、state、status）
   - 检查状态值数量是否 ≥ 3 个，或是否存在状态流转相关的用户故事
   - 如存在，生成业务对象状态机章节（见步骤6章节X）
4. 逐页面定义页面结构和交互事件
5. 细化异常处理和埋点触发时机
6. **将生成的 Markdown 内容直接输出到用户消息中**（如果包含 Mermaid 代码块，需要特殊说明以便渲染）
7. 等待用户确认交付物

---

## 步骤6 输出模板结构

### X. 业务对象状态机

> 仅当业务对象具有复杂状态流转时生成此章节。自动检测规则：属性包含"状态"关键词 + 状态值 ≥ 3 个

#### X.1 {业务对象名称} 状态机

**状态定义表**

| 状态码 | 状态名称 | 状态描述 | 初始状态 | 终态 |
|--------|----------|----------|----------|------|
| {状态码1} | {状态名称1} | {状态描述1} | ✓ | ✗ |
| {状态码2} | {状态名称2} | {状态描述2} | ✗ | ✗ |
| {状态码3} | {状态名称3} | {状态描述3} | ✗ | ✓ |
| {状态码4} | {状态名称4} | {状态描述4} | ✗ | ✓ |

**状态流转表**

| 当前状态 | 触发事件 | 目标状态 | 流转条件 | 异常处理 |
|----------|----------|----------|----------|----------|
| {状态码1} | {事件名称} | {目标状态} | {条件描述} | {异常处理} |
| {状态码2} | {事件名称} | {目标状态} | {条件描述} | {异常处理} |
| {状态码3} | {事件名称} | {目标状态} | {条件描述} | - |

**状态流转图（Mermaid）**

```mermaid
stateDiagram-v2
  [*] --> {初始状态码}
  {状态码1} --> {状态码2}: {触发事件1}
  {状态码1} --> {状态码3}: {触发事件2}
  {状态码2} --> {终态状态码}: {触发事件3}
  {状态码3} --> {终态状态码}: {触发事件4}
  {终态状态码} --> [*]
```

**关键规则说明**
- {规则1}
- {规则2}

### 5. 页面结构和事件定义

> **事件复杂度分级**：
> - **L1 简单**：查询类、刷新类事件 → 使用精简模式
> - **L2 中等**：表单提交、数据更新类事件 → 使用精简模式 + 完整异常处理
> - **L3 复杂**：兑换流程、支付流程等多状态事件 → 使用完整模式
>
> **状态机说明**：状态机流转由第 X 章「业务对象状态机」统一维护，事件描述中只说明**目标状态**。

#### 5.1 {页面名称} (/{pageNameInEN})

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

**事件编号规范：`/{pageNameInEN}/feat-{序号}`**

**L1/L2 精简模式**（简单/中等复杂度事件）：

```
| 事件编号 | 事件名称 | 触发条件 | 数据来源 | 数据去向 | 关联埋点 | 关联用户故事 |
|---------|---------|---------|---------|---------|---------|-------------|
| /home/feat-01 | 查询会员信息 | 页面 onLoad 且登录态有效 | 会员中台接口 | 页面展示+本地缓存 | TRACK-001 | US-001 |
| /home/feat-02 | 刷新商品列表 | 用户下拉刷新 | 本地缓存+接口 | 页面展示 | - | US-002 |
```

**L3 完整模式**（复杂事件）：

```
| 事件编号 | 事件名称 | 事件类型 | 绑定页面 | 触发条件 | 数据来源 | 前端职责 | 后端职责 | 数据去向 | 权限要求 | 幂等性 | 关联埋点 | 关联用户故事 |
|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|-------------|
| /goods-detail/feat-02 | 立即兑换 | 用户点击 | 商品详情页 | 按钮点击+前置校验通过 | 商品接口+积分接口 | 1.调用试算接口 2.跳转地址页 | 提供接口+积分校验 | 页面跳转 | 需登录 | 否 | TRACK-005 | US-003 |
```

##### 5.1.3 事件逻辑

针对每个事件，按以下五段式结构详细说明：

```
前置条件 → 数据输入 → 目标状态 → 数据输出 → 异常处理
```

###### 5.1.3.1 {事件编号} 前置条件

**判断优先级与逻辑关系**：
- 判断顺序：1 → 2 → 3（按序号顺序执行，前一项不通过则终止）
- 逻辑关系：AND（所有条件均需满足）

| 序号 | 条件项 | 判断规则 | 不满足时的处理 |
|----|--------|---------|---------------|
| 1 | 登录态校验 | `token` 存在且未过期 | 跳转授权登录页 |
| 2 | 数据依赖 | `/home/feat-00` 已完成 | 等待依赖事件完成或超时提示 |
| 3 | 权限校验 | 用户有当前店铺数据权限 | 展示无权限提示 |

> 仅 L3 复杂事件需要完整的前置条件说明。L1/L2 事件在「触发条件」字段中简要说明即可。

###### 5.1.3.2 {事件编号} 数据血缘

**输入数据**：
| 字段 | 来源 | 传递方式 | 说明 |
|------|------|---------|------|
| brand_code | 全局配置 | 注入请求头 | 当前品牌编码 |
| openId | 登录态 | 全局状态 | 用户唯一标识 |

**输出数据**：
| 字段 | 去向 | 消费方 | 说明 |
|------|------|--------|------|
| points | 页面展示 | 积分组件 | 展示当前积分余额 |
| points | 全局状态 | `userInfo` Store | 供其他页面读取 |
| points | 本地缓存 | `localStorage` | 有效期 5 分钟，减少重复请求 |
| level | 页面展示 | 等级标识 | 展示会员等级图标 |

> 仅 L3 复杂事件需要完整的数据血缘说明。L1/L2 事件在「数据来源」和「数据去向」字段中简要说明即可。

###### 5.1.3.3 {事件编号} 目标状态

| 字段 | 值 | 说明 |
|------|-----|------|
| 订单状态 | "已提交" | 用户提交兑换后，订单状态变更为已提交 |
| 积分状态 | "扣减中" | 调用积分扣减接口后，积分状态变更为扣减中 |

> 目标状态说明事件执行后系统状态变更为何值。状态机流转由第 X 章「业务对象状态机」统一维护。

###### 5.1.3.4 {事件编号} 数据契约

**请求契约**：
| 字段 | 类型 | 必填 | 来源 | 说明 | 示例值 |
|------|------|------|------|------|--------|
| brand_code | string | 是 | 全局配置 | 品牌编码 | "LOREAL" |
| openId | string | 是 | 全局状态 | 用户 openId | "o123456789" |

**响应契约**：
| 字段 | 类型 | 说明 | 映射 UI 位置 | 数据去向 |
|------|------|------|-------------|---------|
| data.points | number | 积分余额 | 首页积分区域 | 页面展示 + 全局状态 + 本地缓存 |
| data.level | string | 会员等级 | 首页等级标识 | 页面展示 |

###### 5.1.3.5 {事件编号} 异常处理

**异常编号规范：`/{pageNameInEN}/feat-{序号}/err-{序号}`**

```
| 异常场景 | 异常编号 | 判断方式 | 前端处理方案 |
|---------|---------|---------|-------------|
| 接口调用失败 | /home/feat-01/err-01 | HTTP != 200 或请求超时 | Toast提示"网络异常，请检查网络后重试"，提供重试按钮 |
| 会员不存在 | /home/feat-01/err-02 | code="MEMBER_NOT_FOUND" | 弹窗提示"您还不是本品牌会员"，引导跳转注册页 |
| 数据格式错误 | /home/feat-01/err-03 | 积分字段为空或非数字 | 静默展示积分为0，上报监控日志 |
```

**前端处理方案说明**：
- 提示文本格式：`提示文案` + `，` + `操作引导`
- 提示方式：Toast（轻提示，用于可自动恢复的错误）/ 弹窗（需用户主动确认）/ 静默（不影响主流程，后台记录）
- 示例：`Toast提示"积分查询失败，请下拉刷新重试"，提供重试按钮`

###### 5.1.3.6 {事件编号} 埋点触发时机

| 埋点编号 | 埋点名称 | 触发时机 | 采集字段 | 上报条件 |
|---------|---------|---------|---------|---------|
| TRACK-001 | 页面曝光-首页 | 接口返回成功且渲染完成 | 用户ID、活动ID、时间 | 每次进入首页触发 |

> 仅当事件关联埋点时才需要此章节。

##### 5.1.4 {事件编号} 完整示例（参考）

**以「查询会员信息 (/home/feat-01)」为例**：

```markdown
**前置条件**：
- 判断顺序：1 → 2（按序号顺序执行）
- 逻辑关系：AND（所有条件均需满足）

| 序号 | 条件项 | 判断规则 | 不满足时的处理 |
|----|--------|---------|---------------|
| 1 | 登录态校验 | token 存在且未过期 | 跳转授权登录页 |

**数据血缘**：
- 输入：brand_code（全局配置）、openId（全局状态）
- 输出：points → 页面展示+全局状态+本地缓存；level → 页面展示

**目标状态**：无（查询类事件不涉及状态变更）

**数据契约**：
- 请求：brand_code, queryType="OPENID", value=openId
- 响应：data.points, data.level, data.expireDate

**异常处理**：
| 异常场景 | 异常编号 | 判断方式 | 前端处理方案 |
|---------|---------|---------|-------------|
| 接口调用失败 | /home/feat-01/err-01 | HTTP != 200 | Toast提示"积分查询失败，请下拉刷新重试"，提供重试按钮 |
| 会员不存在 | /home/feat-01/err-02 | code="MEMBER_NOT_FOUND" | 弹窗提示"您还不是本品牌会员"，引导跳转注册页 |

**埋点**：TRACK-001 页面曝光-首页，触发时机=接口返回成功
```

---

## 步骤7 输出文件

### 7.1 文件追加逻辑

detail节点完成后，将内容追加到当前版本的PRD文档对应章节：

**PRD章节映射：**
- "X. 业务对象状态机"章节：仅当存在复杂状态机业务对象时生成
- "详细设计"章节（## 3）- 事件列表：Detail 节点细化，与 Analysis 节点整合
- "详细设计"章节（## 3）- 异常场景：Detail 节点补充事件级异常处理
- "详细设计"章节（## 3）- 埋点方案：Detail 节点补充事件级埋点触发时机
- "页面详细设计"章节（## 4）：页面结构和事件定义

**追加规则：**
1. 读取当前PRD文档
2. Detail 节点产出追加到对应章节（不是简单追加，是**整合**）
3. **如果PRD中已存在对应章节，则替换该章节内容**
4. 保持第1、2章节内容不变

### 7.2 输出路径

- 路径: `./{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md`
- 操作模式: **智能追加/替换**（不是简单追加，需判断章节是否存在）

---

## 步骤8 Penpot 绘制

在文档输出完成后，将 PRD 内容绘制到 Penpot 画布上。

### 8.1 连接 Penpot MCP 前的必要准备

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

### 8.2 创建/获取 Penpot 页面

1. 检查 Memory.json 中 `penpot.pages.prd` 是否为空
2. 若为空：
   - **调用API创建新页面**：
     ```
     POST https://design.penpot.app/api/rpc/command/add-page
     Authorization: Token ${PENPOT_ACCESS_TOKEN}
     Content-Type: application/json

     {
       "fileId": "{penpot.fileId}",
       "name": "PRD"
     }
     ```
   - 保存响应中的 `pageId`
   - 更新 Memory.json：`penpot.pages.prd = pageId`
3. 若已存在：
   - 通过MCP `execute_code` 切换到该页面
   - 清除页面上的旧内容（从后往前遍历删除）

### 8.3 整体布局策略（横向多栏 + 按大标题分栏）

**页面布局结构**：
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

**根容器（横向排列所有栏）**：
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
| 业务对象状态机 | 章节标题 + 状态定义表 + 状态流转表 + 状态节点流程图 | 栏1 |
| 页面结构和事件定义 | 章节标题 + 嵌套子容器（每个页面一个） | 栏2 |

> 其他章节（业务背景、用户故事、业务流程图、页面列表、埋点方案）由对应节点绘制，Detail 节点只绘制其独有产出。

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
- 栏1: 业务对象状态机
- 栏2: 页面结构和事件定义

请打开Penpot页面查看并确认。确认后将继续进入GenPrototype节点。
```

> 其他章节（业务背景、用户故事、业务流程图、页面列表、埋点方案）由对应节点绘制。

---

## 步骤9 完成后

1. 读取当前版本PRD文档：`./{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md`
2. 将 Detail 节点产出整合到对应章节：
   - X. 业务对象状态机（如有）
   - ## 3 详细设计 - 事件列表（与 Analysis 节点整合）
   - ## 3 详细设计 - 异常场景（补充事件级异常处理）
   - ## 3 详细设计 - 埋点方案（补充事件级埋点触发时机）
   - ## 4 页面详细设计 - 页面结构和事件定义
3. 保存更新后的PRD文档
4. 调用 manage.update_node_output("detail", "{项目名称}_{datetime}/output/V{Version}/PRD_{Version}.md")
5. 展示产出摘要
6. **执行步骤8 Penpot 绘制**
7. 等待用户确认
8. 确认后 → 调用 manage.update_node_status("detail", "CONFIRM")
9. 确认后 → 调用 manage.transition_to_next_node()
10. 确认后 → 调用 manage.persist_memory()
11. 进入 GenPrototype 节点
