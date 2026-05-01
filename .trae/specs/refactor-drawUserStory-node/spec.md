# Detail 节点 Penpot 集成迭代计划

## 目标

在 detail 节点完成文档输出后，将 PRD 文档内容绘制到 Penpot 画布上。

## 背景

* 当前工作流：router → brainstorm → clarify → analysis → drawUserStory → **detail** → genPrototype → genHTML → change

* drawUserStory 节点已在 Penpot 中绘制用户故事

* genPrototype 节点已在 Penpot 中绘制页面原型

* **detail 节点目前仅输出 Markdown 文档，缺少 Penpot 可视化环节**

## 需求要点

1. 在 Penpot 当前项目中创建新页面
2. 通过容器包裹内容，结构类似 DOM 树：

   * PRD 整体内容包裹在一个根容器

   * 相同标题的内容包裹在容器内

   * 同一层级的多个容器包裹在父容器内
3. 绘制前，引导 LLM 读取 penpot-helper-skill
4. 支持需求变更时的增量更新：

   * 通过标题定位需要变更的内容

   * 添加内容后避免元素遮挡
5. **横向展示**：使用矩形、菱形和箭头横向展示完整内容
6. **模拟表格**：用 Rectangle + Text 组合模拟表格样式
7. **多栏组织**：允许横向多栏组织内容，按大标题分栏

***

## 实施步骤

### 步骤 1：修改 `06-detail.md` 节点文件

在现有 detail 节点流程中，增加 Penpot 绘制环节：

**插入位置**：在「步骤7 输出文件」之后、「步骤8 完成后」之前

**新增步骤：步骤7.5 Penpot 绘制**

#### 7.5.1 加载 penpot-helper-skill

* 在绘制前，读取 `penpot-helper-skill/SKILL.md`

* 重点参考：

  * `references/mcp-generation-guide.md`（坐标系统、ES5 兼容）

  * `references/user-story-layout-guide.md`（Flex 布局防重叠）

  * `references/layout-system.md`（Flex/Grid 属性）

#### 7.5.2 创建/获取 Penpot 页面

* 检查 Memory.json 中 `penpot.pages.prd` 是否为空

* 若为空：

  * 调用 `penpot.createPage()` 创建新页面

  * 页面命名：`PRD 详细设计 - V{version}`

  * 更新 Memory.json：`penpot.pages.prd = 新页面ID`

* 若已存在：

  * 调用 `penpot.openPage()` 切换到该页面

  * 清除页面上的旧内容（从后往前遍历删除）

#### 7.5.3 整体布局策略（横向多栏 + 按大标题分栏）

**页面布局结构**：

```
Penpot Page
├── 栏1: 业务背景和需求概述 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   ├── 子容器: 需求背景
│   └── 子容器: 需求目标
├── 栏2: 用户故事拆分 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   ├── 子容器: Epic 列表
│   └── 子容器: 用户故事表格
├── 栏3: 业务流程图 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   └── 子容器: 流程图绘制区
├── 栏4: 页面流程图 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   └── 子容器: 页面跳转流程
├── 栏5: 页面列表整合 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   ├── 子容器: C 端页面表格
│   └── 子容器: B 端页面表格
├── 栏6: 页面结构和事件定义 (Board, flex column, 固定宽度)
│   ├── 章节标题
│   ├── 子容器: 页面1
│   ├── 子容器: 页面2
│   └── ...
└── 栏7: 埋点方案 (Board, flex column, 固定宽度)
    ├── 章节标题
    └── 子容器: 埋点内容
```

**根容器（横向排列所有栏）**：

```javascript
var rootBoard = penpot.createBoard();
rootBoard.name = "PRD 详细设计 - V" + version;
rootBoard.resize(3200, 1400); // 横向宽，纵向适中
rootBoard.fills = [{ fillColor: "#F1F5F9", fillOpacity: 1 }];
var rootFlex = penpotUtils.addFlexLayout(rootBoard, "row"); // 横向排列
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
columnBoard.resize(420, 1200); // 固定宽度，高度自适应
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

#### 7.5.4 流程图绘制规范（矩形 + 菱形 + 箭头横向展示）

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

// 判断节点（菱形 - 条件判断）
var decisionRect = penpot.createRectangle();
decisionRect.name = "判断: 积分足够?";
decisionRect.resize(160, 80);
decisionRect.fills = [{ fillColor: "#FFF3E0", fillOpacity: 1 }];
decisionRect.borderRadius = 8; // Penpot 不支持真正的菱形，用带圆角的矩形表示
decisionRect.strokes = [{ strokeColor: "#FF9800", strokeWidth: 1, strokeStyle: "solid" }];

// 箭头（用细长的 Rectangle 模拟，或使用 Path）
var arrow = penpot.createRectangle();
arrow.name = "箭头";
arrow.resize(2, 24);
arrow.fills = [{ fillColor: "#90A4AE", fillOpacity: 1 }];
```

**流程图布局**：使用 Flex column 纵向排列，每个节点之间用箭头连接。节点横向居中对齐。

#### 7.5.5 表格模拟规范（Rectangle + Text 组合）

**表格容器**：

```javascript
var tableBoard = penpot.createBoard();
tableBoard.name = "表格: 用户故事";
tableBoard.resize(360, 300);
tableBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
tableBoard.borderRadius = 4;
tableBoard.strokes = [{ strokeColor: "#E2E8F0", strokeWidth: 1, strokeStyle: "solid" }];
var tableFlex = penpotUtils.addFlexLayout(tableBoard, "column");
tableFlex.rowGap = 0; // 行间距由单元格边框控制
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

#### 7.5.6 内容映射规则（更新后）

| PRD 章节    | 绘制方式                                  | 所在栏 |
| --------- | ------------------------------------- | --- |
| 业务背景和需求概述 | 章节标题 + 段落文本                           | 栏1  |
| 用户故事拆分    | 章节标题 + **模拟表格**（Board+Rectangle+Text） | 栏2  |
| 业务流程图     | 章节标题 + **矩形+菱形+箭头流程图**                | 栏3  |
| 页面流程图     | 章节标题 + **横向箭头流程**                     | 栏4  |
| 页面列表整合    | 章节标题 + **模拟表格**                       | 栏5  |
| 页面结构和事件定义 | 章节标题 + 嵌套子容器（每个页面一个）                  | 栏6  |
| 埋点方案      | 章节标题 + 段落文本                           | 栏7  |

#### 7.5.7 防止重叠策略

1. **根容器使用 Flex row**：所有栏横向排列，自动处理间距
2. **单栏使用 Flex column**：栏内内容纵向排列
3. **表格使用固定尺寸单元格**：确保表格不溢出
4. **流程图节点固定尺寸**：矩形 200×48，菱形 160×80
5. **嵌套深度控制**：最多 4 层 Board（根 → 栏 → 子容器 → 单元格/节点）
6. **绘制后验证**：

   * 检查所有元素都在父容器边界内

   * 检查栏与栏之间无重叠（columnGap > 0）

   * 检查表格单元格对齐正确

### 步骤 2：更新 `_manage.md` Memory.json 结构

确认 `penpot.pages` 已包含 `prd` 字段（已存在，无需修改）：

```json
{
  "penpot": {
    "pages": {
      "userStory": "",
      "prototype": "",
      "prd": ""
    }
  }
}
```

### 步骤 3：更新 `09-change.md` 变更处理

在变更节点的 Penpot 同步策略中，增加 `prd` 页面的处理：

| 变更场景     | 同步方式             | 操作               |
| -------- | ---------------- | ---------------- |
| PRD 内容变更 | 按栏标题定位 Board     | 替换栏内的子容器内容       |
| 新增章节     | 在根容器中追加新栏 Board  | 使用 Flex row 自动排列 |
| 删除章节     | 按名称定位栏 Board 并移除 | 后续栏自动左移          |
| 表格内容变更   | 按表格名称定位，替换数据行    | 保持表头不变           |
| 流程图变更    | 按流程图名称定位，重新绘制节点  | 清除旧节点，绘制新节点      |

**避免重叠策略**：

* 更新时保持栏容器结构不变，只替换内部子容器

* 新增栏时，直接 appendChild 到根容器，Flex 自动排列

* 删除栏时，调用 `board.remove()`，Flex 自动收缩

* 表格更新时，只替换数据行，保持表头和列宽

### 步骤 4：更新 `01-router.md` 节点概览

在节点概览表中，更新 detail 节点的说明：

| 序号 | 节点     | 用户确认 | 说明                               |
| -- | ------ | ---- | -------------------------------- |
| 06 | detail | ✓    | 详细设计(流程图) + PRD Penpot 可视化(横向多栏) |

### 步骤 5：更新 `SKILL.md` 工作流说明

在工作流图和节点映射表中，更新 detail 节点的描述：

* 详细设计：业务流程图、页面流程图、事件列表、异常场景

* **新增：PRD 内容 Penpot 可视化（横向多栏、流程图、模拟表格）**

***

## 文件变更清单

| 文件                   | 操作 | 说明                               |
| -------------------- | -- | -------------------------------- |
| `nodes/06-detail.md` | 修改 | 增加步骤7.5 Penpot 绘制环节（横向多栏、流程图、表格） |
| `nodes/_manage.md`   | 确认 | 确认 Memory.json 结构已支持 prd 页面      |
| `nodes/09-change.md` | 修改 | 增加 prd 页面的变更同步策略（栏级别、表格、流程图）     |
| `nodes/01-router.md` | 修改 | 更新 detail 节点说明                   |
| `SKILL.md`           | 修改 | 更新工作流和节点说明                       |

***

## 风险与应对

| 风险             | 影响     | 应对措施                           |
| -------------- | ------ | ------------------------------ |
| 栏数过多导致页面宽度超出   | 内容被截断  | 控制每栏宽度 400px 左右，最多 6-8 栏；超出时分行 |
| 表格列宽不一致        | 可读性差   | 统一定义列宽常量，所有表格使用相同列宽            |
| 流程图节点过多导致栏高度不足 | 节点重叠   | 栏高度使用 auto，或限制每栏最多显示 10 个节点    |
| 菱形判断节点绘制复杂     | 实现困难   | 使用带圆角的矩形代替，用橙色边框区分             |
| 箭头连接绘制复杂       | 实现困难   | 使用细长的 Rectangle 模拟垂直箭头         |
| 变更时定位栏失败       | 同步更新失败 | 使用唯一命名的栏 Board 名称（含章节标题）       |

***

## 实施顺序

1. **修改** **`06-detail.md`**：增加 Penpot 绘制步骤（横向多栏、流程图、表格）
2. **更新** **`09-change.md`**：增加 prd 页面变更同步（栏、表格、流程图）
3. **更新** **`01-router.md`**：更新节点说明
4. **更新** **`SKILL.md`**：更新工作流描述
5. **测试验证**：运行 detail 节点，验证 Penpot 绘制效果

