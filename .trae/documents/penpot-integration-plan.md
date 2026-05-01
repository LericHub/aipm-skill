# Penpot 集成实施计划

## 项目现状分析

### 当前项目结构
```
aipm-skill-CN/
├── product_manager/
│   ├── SKILL.md                    # 7节点工作流定义（ROUTER→BRAINSTORM→CLARIFY→ANALYSIS→DETAIL→GENHTML→CHANGE）
│   ├── nodes/
│   │   ├── 01-router.md           # 入口路由器
│   │   ├── 02-brainstorm.md       # 头脑风暴
│   │   ├── 03-clarify.md          # 需求澄清
│   │   ├── 04-analysis.md         # 需求分析
│   │   ├── 05-detail.md           # 详细设计（原编号，实际应为06）
│   │   ├── 07-genHTML.md          # HTML原型生成（原编号，实际应为08）
│   │   ├── 08-change.md           # 变更分析（原编号，实际应为09）
│   │   └── _manage.md             # 状态管理、Memory.json、版本控制
│   └── references/
│       └── knowledge_cards/       # 7个知识卡片
├── penpot-helper-skill/           # Penpot辅助技能（已有）
│   ├── SKILL.md                   # Penpot布局指南、Plugin API、MCP Server
│   ├── references/
│   │   ├── mcp-server.md          # MCP Server架构和工具
│   │   ├── mcp-generation-guide.md # 程序化生成指南（坐标系统、ES5兼容等）
│   │   ├── plugin-api.md          # Plugin SDK API参考
│   │   └── ...                    # 其他参考文档
│   └── templates/                 # 布局模板
└── .trae/documents/
    └── penpot-integration-iteration-plan.md  # 已有迭代计划（但未执行）
```

### 关键发现

1. **节点编号混乱**：现有文件编号与实际流程不匹配
   - `05-detail.md` 实际应为 `06-detail.md`（在drawUserStory之后）
   - `07-genHTML.md` 实际应为 `08-genHTML.md`
   - `08-change.md` 实际应为 `09-change.md`

2. **Penpot集成计划已存在但未执行**：`.trae/documents/penpot-integration-iteration-plan.md` 详细描述了集成方案，但节点文件未按此更新

3. **MCP能力已具备**：`penpot-helper-skill` 已包含完整的MCP Server文档，支持 `execute_code`、`export_shape` 等工具

4. **Memory.json已有penpot字段**：`_manage.md` 中已定义了 `penpot.projectUrl`、`fileId`、`pages` 结构

---

## 用户需求梳理

### 核心需求
1. **DrawUserStory节点**：创建新的Penpot项目，在该项目的不同页面上生成内容
2. **genPrototype节点**：在Penpot项目上新建一个页面生成原型
3. **genHTML节点**：根据设计稿生成HTML

### 补充需求
1. **API Token**：确认MCP是否有创建页面、创建项目的能力
2. **项目-页面映射**：一个skill项目对应一个penpot项目文件（在DrawUserStory创建）；DrawUserStory、detail、genPrototype三个节点都会创建新的页面
3. **需求变更处理**：
   - 最小改动范围（按名称定位修改）
   - 避免元素重叠遮盖
   - 需要确认逻辑可行性及落地方式

---

## 实施计划

### 阶段一：MCP能力调研与确认

**目标**：确认Penpot MCP Server的能力边界，特别是创建项目/页面的能力

**步骤**：
1. 阅读 `penpot-helper-skill/references/mcp-server.md` 和 `plugin-api.md`
2. 确认MCP工具列表：`execute_code`、`high_level_overview`、`penpot_api_info`、`export_shape`、`import_image`
3. **关键发现**：MCP Server本身**没有直接创建项目/页面的REST API工具**，而是通过 `execute_code` 在Penpot Plugin环境中执行JS代码来操作
4. **创建页面的方式**：通过 `execute_code` 调用 `penpot.createPage()` 或操作 `penpot.currentFile` 来创建页面
5. **创建项目的方式**：Penpot MCP Server是连接到**已有**的Penpot文件，不能独立创建新项目文件。需要在Penpot UI中手动创建文件，然后通过file ID连接

**结论**：
- ✅ 可以在已有Penpot文件中创建新页面（通过 `execute_code` 调用 Plugin API）
- ❌ 不能通过MCP独立创建新的Penpot项目文件
- **方案**：DrawUserStory节点指导用户在Penpot中创建文件，或复用已有文件，然后通过MCP在该文件中创建页面

---

### 阶段二：节点文件重构

**目标**：修正节点编号，新增DrawUserStory和genPrototype节点

**步骤**：
1. **新增 `05-drawUserStory.md`**：用户故事可视化节点
   - 读取analysis产出的PRD文档
   - 解析用户故事列表
   - 在Penpot中创建/获取用户故事页面
   - 绘制用户故事容器结构（Flex布局）
   - 保存页面ID到Memory.json

2. **重命名 `05-detail.md` → `06-detail.md`**：详细设计节点
   - 更新前序节点引用（从analysis改为drawUserStory）
   - 更新后续节点引用（进入genPrototype）

3. **新增 `07-genPrototype.md`**：页面原型设计节点
   - 读取PRD文档中的页面列表
   - 在Penpot中创建/获取原型设计页面
   - 绘制页面设计稿（手机尺寸375×812）
   - 保存页面ID到Memory.json

4. **重命名 `07-genHTML.md` → `08-genHTML.md`**：HTML原型生成节点
   - 增强：优先基于Penpot设计稿生成HTML
   - 保留TDesign组件生成作为备选

5. **重命名 `08-change.md` → `09-change.md`**：变更分析节点
   - 增加Penpot画布同步更新策略

6. **更新 `01-router.md`**：更新节点流转顺序和概览表

7. **更新 `_manage.md`**：确认Memory.json结构已支持penpot字段

8. **更新 `SKILL.md` 和 `README.md`**：更新工作流图和节点说明

---

### 阶段三：Penpot页面管理策略设计

**目标**：设计一个skill项目对应一个penpot项目的映射关系

**方案**：

#### 1. 项目创建策略
```
Skill项目启动（Brainstorm/Clarify完成后）
    ↓
DrawUserStory节点首次执行时：
    ↓
检查 Memory.json 中 penpot.fileId 是否为空
    ↓
    ├── 为空 → 提示用户在Penpot中创建新文件
    │          获取文件URL/ID，保存到Memory.json
    │          在该文件中创建「用户故事」页面
    │
    └── 有值 → 直接在该文件中创建/切换到「用户故事」页面
```

#### 2. 页面创建规则
| 节点 | 页面名称 | 页面用途 |
|------|---------|---------|
| DrawUserStory | `用户故事 - V{version}` | 绘制用户故事业务流程和页面流程 |
| Detail | `详细设计 - V{version}` | 绘制详细业务流程图、页面流程图 |
| GenPrototype | `页面原型 - V{version}` | 绘制页面设计稿 |

#### 3. Memory.json结构（已存在，需确认完整）
```json
{
  "penpot": {
    "projectUrl": "https://design.penpot.app/#/workspace?team-id=...&file-id=...",
    "fileId": "uuid",
    "pages": {
      "userStory": "page-uuid",
      "detail": "page-uuid",
      "prototype": "page-uuid"
    }
  }
}
```

---

### 阶段四：需求变更处理策略

**目标**：实现最小化改动的Penpot画布更新机制

#### 1. 定位策略
| 变更场景 | 定位方式 | 操作 |
|---------|---------|------|
| 用户故事变更 | 按Board名称定位（`用户故事: XXX`） | 替换Board内的文本元素 |
| 页面设计变更 | 按Board名称定位（`页面: XXX`） | 清除旧设计稿后重新绘制 |
| 仅文案修改 | 按Text名称定位 | 修改 `text.characters` 属性 |
| PRD内容变更 | 按章节标题定位 | 替换对应章节内容 |

#### 2. 避免重叠策略
- **使用Flex布局**：所有容器使用Flex布局，自动排列子元素
- **更新时保持容器结构**：只替换内部文本/元素，不重新创建容器
- **清除旧内容时从后往前遍历**：`for (var i = kids.length-1; i >= 0; i--) kids[i].remove()`
- **验证无重叠**：每次绘制后检查所有元素边界

#### 3. 版本变更时的Penpot处理
- **Minor版本变更（V1.0 → V1.1）**：在现有页面上更新内容
- **Major版本变更（V1.x → V2.0）**：创建新页面（如 `用户故事 - V2.0`），保留旧页面

---

### 阶段五：节点内容详细设计

#### DrawUserStory节点（05-drawUserStory.md）

**执行逻辑**：
1. 读取analysis节点的PRD文档，提取用户故事列表
2. 检查Memory.json中 `penpot.pages.userStory`
3. 若为空：
   - 检查 `penpot.fileId` 是否为空
   - 若为空：提示用户创建Penpot文件并提供fileId
   - 调用 `execute_code` 创建新页面，命名 `用户故事 - V{version}`
   - 保存page ID到Memory.json
4. 若已存在：切换到该页面，清除旧内容
5. 绘制用户故事容器：
   ```
   所有用户故事 (Board, flex column)
   └── 用户故事: XXX (Board, flex column)
       ├── 标题文本 (Text)
       ├── 描述文本 (Text)
       └── 流程容器 (Board, flex row)
           ├── 业务流程 (Board, flex column)
           └── 页面流程 (Board, flex column)
   ```
6. 验证无重叠，通知用户

#### GenPrototype节点（07-genPrototype.md）

**执行逻辑**：
1. 读取detail节点的PRD文档，提取页面列表
2. 检查Memory.json中 `penpot.pages.prototype`
3. 若为空：创建新页面 `页面原型 - V{version}`
4. 若已存在：切换到该页面，清除旧内容
5. 绘制页面设计稿：
   ```
   所有页面原型 (Board, flex column)
   └── 页面: XXX (Board, flex column, 375×812)
       ├── 页面标题 (Text)
       ├── 功能区域1 (Board)
       ├── 功能区域2 (Board)
       └── 操作按钮区 (Board, flex row)
   ```
6. 验证无重叠，通知用户

#### GenHTML节点（08-genHTML.md）

**增强逻辑**：
1. 检查Memory.json中 `penpot.pages.prototype`
2. 若存在：
   - 切换到原型页面
   - 遍历所有页面Board
   - 对每个页面调用 `generateMarkup()` 和 `generateStyle()`
   - 合并为完整HTML文件
3. 若不存在：使用TDesign组件生成（方式B）
4. 生成index.html入口页面
5. 打包为zip

---

### 阶段六：文档更新

1. **更新 `SKILL.md`**：
   - 工作流图增加DrawUserStory和genPrototype节点
   - 节点映射表更新

2. **更新 `product_manager/nodes/README.md`**：
   - 节点概览表更新
   - 目录结构说明更新

3. **更新 `_manage.md`**：
   - 节点流转顺序更新
   - Memory.json结构确认（penpot字段已存在）

---

## 文件变更清单

| 文件 | 操作 | 说明 |
|------|------|------|
| `nodes/05-drawUserStory.md` | 新增 | 用户故事绘制节点 |
| `nodes/05-detail.md` | 重命名→`06-detail.md` | 编号修正，更新前后引用 |
| `nodes/07-genPrototype.md` | 新增 | 页面原型设计节点 |
| `nodes/07-genHTML.md` | 重命名→`08-genHTML.md` | 编号修正，增强Penpot读取 |
| `nodes/08-change.md` | 重命名→`09-change.md` | 编号修正，增加Penpot同步策略 |
| `nodes/01-router.md` | 修改 | 更新节点流转顺序和概览表 |
| `nodes/_manage.md` | 修改 | 更新节点流转顺序，确认Memory.json结构 |
| `SKILL.md` | 修改 | 更新工作流图和节点说明 |
| `README.md` | 修改 | 更新节点说明和目录结构 |

---

## 风险与应对

| 风险 | 影响 | 应对措施 |
|------|------|---------|
| Penpot MCP无法创建项目文件 | DrawUserStory无法自动创建项目 | 指导用户手动创建文件并提供fileId |
| 设计稿转HTML质量差 | 原型不可用 | 保留TDesign生成作为备选（方式B） |
| 大量元素性能问题 | 页面卡顿 | 分页绘制，限制单次元素数量 |
| 坐标计算错误导致重叠 | 视觉效果差 | 使用Flex布局，绘制后验证边界 |
| 用户未连接Penpot | 功能不可用 | 提供降级方案（仅文本输出） |

---

## 实施顺序

1. **第一周**：阶段一（MCP能力确认）+ 阶段二（节点文件重构）
2. **第二周**：阶段三（页面管理策略）+ 阶段四（变更处理策略）
3. **第三周**：阶段五（节点内容详细设计）+ 阶段六（文档更新）
4. **第四周**：测试验证 + 修复优化
