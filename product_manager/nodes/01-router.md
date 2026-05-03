---
name: 01-router
description: 每次对话开始时进入。在此节点判断用户意图并路由到对应节点，是工作流的统一入口。
---

# 01 - Router (入口路由器)

## 步骤1 意图判断与路由分发

每次对话开始时，Router 是唯一的入口。LLM 必须首先执行以下判断逻辑：

### 1.1 检查 Memory.json

1. 检查项目根目录是否存在 `Memory.json`
2. **若不存在** → 判定为「新项目」，进入 **步骤1.2**
3. **若存在** → 读取 `Memory.json`，获取 `current_node` 和节点状态，进入 **步骤1.3**

### 1.2 新项目路径

- 用户首次提出需求，无历史项目状态
- **路由目标**：brainstorm 节点
- **动作**：
  1. 加载知识卡片（见步骤2）
  2. 进入 brainstorm 节点执行

### 1.3 继续项目路径

- 已有 Memory.json，需要判断用户当前意图

**意图识别规则**：

| 用户输入关键词 | 判定意图 | 路由目标 |
|--------------|---------|---------|
| "确认"、"没问题"、"可以"、"好的"、"继续" | 推进流程 | current_node（继续当前节点或进入下一节点） |
| "修改"、"不对"、"重来"、"重新生成" | 修改当前 | current_node（重新执行） |
| "变更"、"加功能"、"改需求"、"新增"、"删除" | 需求变更 | change 节点 |
| 其他新需求描述 | 新项目 | brainstorm 节点（需确认是否覆盖旧项目） |

**路由逻辑**：
1. 提取用户输入的核心意图
2. 匹配上表中的判定规则
3. 若匹配到「推进流程」或「修改当前」：
   - 读取 `current_node` 的状态
   - 若当前节点为 `DRAFT` → 继续执行当前节点
   - 若当前节点为 `CONFIRM` → 流转到下一节点（调用 manage.transition_to_next_node()）
4. 若匹配到「需求变更」→ 进入 change 节点
5. 若无法识别意图 → 询问用户意图

### 1.4 路由分发表

| 条件 | 目标节点 | 说明 |
|------|---------|------|
| Memory.json 不存在 | brainstorm | 新项目，首次启动 |
| current_node = brainstorm, 状态 = DRAFT | brainstorm | 继续头脑风暴 |
| current_node = brainstorm, 状态 = CONFIRM | clarify | brainstorm 已完成，进入澄清 |
| current_node = clarify, 状态 = DRAFT | clarify | 继续需求澄清 |
| current_node = clarify, 状态 = CONFIRM | analysis | clarify 已完成，进入分析 |
| current_node = analysis, 状态 = DRAFT | analysis | 继续需求分析 |
| current_node = analysis, 状态 = CONFIRM | drawUserStory | analysis 已完成，进入用户故事 |
| current_node = drawUserStory, 状态 = DRAFT | drawUserStory | 继续用户故事绘制 |
| current_node = drawUserStory, 状态 = CONFIRM | detail | drawUserStory 已完成，进入详细设计 |
| current_node = detail, 状态 = DRAFT | detail | 继续详细设计 |
| current_node = detail, 状态 = CONFIRM | genPrototype | detail 已完成，进入原型设计 |
| current_node = genPrototype, 状态 = DRAFT | genPrototype | 继续原型设计 |
| current_node = genPrototype, 状态 = CONFIRM | genHTML | genPrototype 已完成，进入 HTML 生成 |
| current_node = genHTML, 状态 = DRAFT | genHTML | 继续 HTML 生成 |
| current_node = genHTML, 状态 = CONFIRM | 项目完成 | 所有节点已完成 |
| 用户说"变更"/"改需求" | change | 进入变更分析 |

---

## 步骤2 统一知识卡片加载

Router 负责为所有节点统一加载知识卡片。业务节点不再重复执行此步骤。

### 2.1 加载流程

1. **每轮固定读取索引**：读取 `product_manager/references/knowledge_cards/index.md`
2. **识别用户输入关键词**：提取用户当前输入中的核心话题/关键词
3. **由 LLM 决定卡片选择**：
   - 参考 index 中的卡片目录、激活关键词、适用节点和使用规则
   - 结合用户输入和目标节点，自主决定需要读取哪些卡片
4. **加载卡片内容**：读取选中的卡片完整内容
5. **传递给目标节点**：将加载的知识卡片内容作为上下文传递给路由目标节点

### 2.2 选择约束

- Router 是唯一允许定义知识卡片入口行为的节点
- 业务节点只消费 Router 提供的知识卡片上下文，不再定义独立加载策略
- `knowledge_cards/index.md` 是 Router 选择卡片时的唯一索引入口

---

## 步骤3 职责

- **意图判断**：识别用户是「新项目」、「继续推进」、「修改当前」还是「需求变更」
- **路由分发**：根据意图和 Memory.json 状态，将对话路由到正确的节点
- **知识卡片加载**：统一为所有节点加载相关知识卡片
- **全局规范定义**：定义节点执行规范
- **节点概览提供**：输出当前项目状态和节点进度表

---

## 步骤4 执行规范

1. **节点线性执行**: router → brainstorm → clarify → analysis → drawUserStory → detail → genPrototype → genHTML → change
2. **用户确认**: 所有节点完成后需确认
3. **状态管理**: 由 `_manage` 内部能力统一处理（状态更新、快照持久化、版本管理）
4. **目录初始化**: 在 brainstorm 节点首次需要写文件时创建（第一个需要写文件的节点）

---

## 步骤5 流程强制规则

### 节点流转规则

**绝对禁止：**

- 禁止跳过任何节点
- 禁止在未确认的情况下前进
- 禁止直接跳到最终节点
- 禁止忘记检查 Memory.json
- 禁止不输出头部信息

**必须执行：**

- 必须：每次对话前检查 Memory.json → 读取状态 → 确定节点
- 必须：输出头部信息
- 必须：等待用户确认才能前进
- 必须：严格按节点顺序执行
- 必须：立即持久化状态变更

### 节点完成判定

**节点完成的判定标准（全部满足）：**

1. 用户明确说"确认"、"没问题"、"可以"、"好的"、"继续"
2. Memory.json 中对应节点状态为 `CONFIRM`
3. 对应节点产出已写入 `node_list[].output`
4. 已输出完整的节点产出物

### 用户反馈判定

**验收通过意图（可进入下一节点）：**

| 意图类型 | 用户说 | 判定 | 动作 |
|---------|--------|------|------|
| 明确确认 | "确认"、"没问题"、"可以"、"好的"、"通过"、"验收通过" | CONFIRM | 进入下一节点 |
| 推进指令 | "继续"、"下一步"、"往下走"、"进入下一阶段" | CONFIRM | 进入下一节点 |
| 满意表达 | "满意"、"就这样"、"定稿"、"OK"、"ok" | CONFIRM | 进入下一节点 |

**非验收意图（停留在当前节点）：**

| 意图类型 | 用户说 | 判定 | 动作 |
|---------|--------|------|------|
| 修改请求 | "修改"、"不对"、"重来"、"重新生成"、"调整"、"改一下" | MODIFY | 重新执行当前节点 |
| 补充需求 | "补充"、"还有"、"另外"、"加上"、"漏了" | MODIFY | 补充内容后重新展示 |
| 疑问咨询 | "为什么"、"什么意思"、"不懂"、"解释一下" | MODIFY | 解答疑问后继续等待确认 |
| 部分确认 | "这部分可以"、"其他的再改改" | MODIFY | 视为未完全确认，继续修改 |
| 拖延/犹豫 | "再看看"、"先这样"、"待定" | MODIFY | 明确询问用户是否确认 |
| 变更需求 | "变更"、"加功能"、"改需求"、"新增"、"删除" | CHANGE | 进入变更分析 |

**重要规则：**
- **只有用户明确表达验收通过意图时，才能推进到下一节点**
- **任何非验收意图（修改、补充、疑问、部分确认、犹豫）都必须停留在当前节点，不得进入下一节点**
- 用户表达"变更"、"加功能"、"改需求"等意图时，进入 change 节点

### CHANGE 节点定位

- CHANGE 是可选节点，不参与主流程计数
- 仅在用户提出"变更"时才进入
- 处理完后回退到受影响的主流程节点

---

## 步骤6 文件生成规则

### 场景 1: 首次产出
- **条件**: 节点首次产出内容
- **操作**: 生成新文件到当前版本目录

### 场景 2: 当前节点修改
- **条件**: 用户在当前节点修改内容
- **操作**: 更新源文件，版本号不变

### 场景 3: 需求变更 + 前序内容修改
- **条件**: 需求变更且前序节点内容需要修改
- **操作**: 生成新文件到新版目录，版本号+1（如 V1.0 → V2.0）

### 目录结构

```
{项目名}_{datetime}/
├── analysis_process/              # 不纳入版本管理
│   ├── brainstorm.md            # 功能骨架
│   └── clarify.md               # brainstorm+clarify整合内容
└── output/                       # 纳入版本管理
    └── {Version}/
        ├── PRD_{Version}.md    # PRD文档（整合所有节点产出）
        └── prototype/           # 原型文件
            ├── index.html
            ├── app/
            │   └── page_*.html
            └── web/
                └── page_*.html
```

---

## 步骤7 内部能力

- `_manage`: 状态管理、记忆持久化、版本管理（see ./_manage.md）

---

## 步骤8 节点概览

| 序号 | 节点 | 用户确认 | 说明 |
|------|------|---------|------|
| 01 | router | - | 入口，每次对话必经 |
| 02 | brainstorm | ✓ | 头脑风暴 |
| 03 | clarify | ✓ | 需求澄清(7维度) |
| 04 | analysis | ✓ | 需求分析(PRD骨架) |
| 05 | drawUserStory | ✓ | 用户故事可视化(PenPot) |
| 06 | detail | ✓ | 详细设计(流程图) + PRD Penpot 可视化(横向多栏) |
| 07 | genPrototype | ✓ | 页面原型设计(PenPot) |
| 08 | genHTML | ✓ | HTML原型生成 |
| 09 | change | ✓ | 变更分析(可选) |

---

## 步骤9 节点详情（简短引用）

- brainstorm: see ./02-brainstorm.md
- clarify: see ./03-clarify.md
- analysis: see ./04-analysis.md
- drawUserStory: see ./05-drawUserStory.md
- detail: see ./06-detail.md
- genPrototype: see ./07-genPrototype.md
- genHTML: see ./08-genHTML.md
- change: see ./09-change.md
