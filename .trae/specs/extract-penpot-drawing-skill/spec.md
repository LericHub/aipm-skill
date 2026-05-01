# 抽取 Penpot 绘制 Skill Spec

## Why

当前 `05-drawUserStory.md` 和 `07-genPrototype.md` 节点文件中包含大量 Penpot 绘制代码（约800行），导致：
- 节点文件臃肿，难以维护
- Penpot 绘制逻辑无法被其他节点复用
- 绘制代码与业务流程耦合

## What Changes

1. **新建** `product_manager/skills/penpot-drawing-skill/` 目录
2. **新建** `penpot-drawing-skill/SKILL.md` - 主skill文件
3. **新建** `penpot-drawing-skill/references/user-story-drawing-guide.md` - 用户故事绘制指引
4. **新建** `penpot-drawing-skill/references/prototype-drawing-guide.md` - 原型绘制指引
5. **修改** `05-drawUserStory.md` - 简化为引用外部skill
6. **修改** `07-genPrototype.md` - 简化为引用外部skill

## Impact

- Affected specs: 无
- Affected files:
  - `product_manager/skills/penpot-drawing-skill/SKILL.md` (新建)
  - `product_manager/skills/penpot-drawing-skill/references/user-story-drawing-guide.md` (新建)
  - `product_manager/skills/penpot-drawing-skill/references/prototype-drawing-guide.md` (新建)
  - `product_manager/nodes/05-drawUserStory.md` (修改)
  - `product_manager/nodes/07-genPrototype.md` (修改)

---

## 新增文件内容说明

### 1. SKILL.md（主skill文件）

**职责**：协调何时使用哪个绘制指引

**内容**：
- **触发条件**：
  - "执行 drawUserStory 节点时"
  - "执行 genPrototype 节点时"
  - "需要在 Penpot 中绘制用户故事或页面原型时"
- **前置条件**：
  - 必须先加载 `penpot-helper-skill/SKILL.md`
  - 必须先读取 `penpot-helper-skill/references/mcp-generation-guide.md`
  - 必须先读取 `penpot-helper-skill/references/user-story-layout-guide.md`
  - 必须先读取 `penpot-helper-skill/references/layout-system.md`
- **指引索引**：
  - 用户故事绘制 → `references/user-story-drawing-guide.md`
  - 原型绘制 → `references/prototype-drawing-guide.md`

---

### 2. references/user-story-drawing-guide.md（用户故事绘制指引）

**职责**：提供 drawUserStory 节点专用的绘制代码

**从 05-drawUserStory.md 抽取的内容**：

| 原章节 | 内容 |
|-------|------|
| 4.4 绘制用户故事容器 | 容器结构定义 |
| 4.4.1 父容器 | "所有用户故事" Board 代码 |
| 4.4.2 用户故事容器 | 单个用户故事 Board 代码 |
| 4.4.3 业务流程图绘制 | 判断节点、分支路径、水平箭头、汇聚节点代码 |
| 4.4.4 页面地图绘制 | 页面节点、跳转箭头、返回路径、多分支跳转代码 |
| 4.5 防止重叠检查 | 验证清单 |

**关键代码片段示例**：
```javascript
// 用户故事容器
var storyBoard = penpot.createBoard();
storyBoard.name = "用户故事: " + storyName;
storyBoard.resize(1300, 700);
storyBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
// ...

// 判断节点
var decisionNode = penpot.createBoard();
decisionNode.name = "判断: " + conditionText;
decisionNode.resize(140, 80);
decisionNode.fills = [{ fillColor: "#FFF3E0", fillOpacity: 1 }];
// ...
```

---

### 3. references/prototype-drawing-guide.md（原型绘制指引）

**职责**：提供 genPrototype 节点专用的绘制代码

**从 07-genPrototype.md 抽取的内容**：

| 原章节 | 内容 |
|-------|------|
| 4.3 绘制页面设计稿 | 容器结构定义 |
| 4.3.1 父容器 | "所有页面原型" Board 代码 |
| 4.3.2 页面容器 | 单个页面 Board 代码（375×812手机尺寸） |
| 4.3.3 页面元素 | 页面标题、功能区域、操作按钮代码 |
| 4.4 绘制内容参考 | PRD内容到Penpot元素的映射规则 |

**关键代码片段示例**：
```javascript
// 页面容器
var pageBoard = penpot.createBoard();
pageBoard.name = "页面: " + pageName;
pageBoard.resize(375, 812); // 手机页面尺寸
pageBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
pageBoard.borderRadius = 12;
// ...
```

---

## 引用关系

| 节点文件 | 引用 skill | 引用 guide |
|---------|-----------|-----------|
| 05-drawUserStory.md | penpot-drawing-skill/SKILL.md | user-story-drawing-guide.md |
| 07-genPrototype.md | penpot-drawing-skill/SKILL.md | prototype-drawing-guide.md |

---

## 抽取原则

1. **不修改绘制逻辑**：只移动位置，不改变代码内容
2. **保持引用关系**：节点文件更新为引用路径
3. **保持目录结构**：新建文件放在 `skills/penpot-drawing-skill/` 下

---

## 修改后节点文件对比

### 05-drawUserStory.md

| 项目 | Before | After |
|-----|--------|-------|
| 文件大小 | ~800行 | ~200行 |
| 包含内容 | 职责+加载+绘制代码+输出 | 职责+加载引用+执行流程+输出 |
| 绘制代码 | 内嵌 | 引用 user-story-drawing-guide.md |

### 07-genPrototype.md

| 项目 | Before | After |
|-----|--------|-------|
| 文件大小 | ~200行 | ~100行 |
| 包含内容 | 职责+加载+绘制代码+输出 | 职责+加载引用+执行流程+输出 |
| 绘制代码 | 内嵌 | 引用 prototype-drawing-guide.md |
