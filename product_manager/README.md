# AIPM 产品经理工作流 Skill

> 从模糊需求到标准 PRD 的完整链路，7 节点标准化流程

---

## 概述

`product_manager` 是一个产品经理全流程工作流 Skill，专注于帮助 AI Agent 完成从需求理解到 PRD 文档交付的完整产品开发流程。

**核心特点：**
- 7 节点标准化流程：路由 → 头脑风暴 → 需求澄清 → 需求分析 → 详细设计 → HTML原型生成 → 变更分析
- 独立变更分析节点 - 增量变更无需整体回退
- 三级用户确认机制
- 完整状态持久化可追溯
- 历史版本永久保留
- 上下文快照管理 - 确保节点间信息传递的一致性
- 目录分离：analysis_process/（不纳入版本管理）和output/（纳入版本管理）

---

## 节点概览

| 序号 | 节点 | 文件 | 用户确认 | 职责 |
|------|------|------|---------|------|
| 01 | ROUTER | [nodes/01-router.md](nodes/01-router.md) | - | 入口路由器，定义全局执行规范 |
| 02 | BRAINSTORM | [nodes/02-brainstorm.md](nodes/02-brainstorm.md) | ✓ | 头脑风暴，功能骨架发散 |
| 03 | CLARIFY | [nodes/03-clarify.md](nodes/03-clarify.md) | ✓ | 需求澄清，7 维度深度挖掘 |
| 04 | ANALYSIS | [nodes/04-analysis.md](nodes/04-analysis.md) | ✓ | 需求分析，PRD 框架构建 |
| 05 | DETAIL | [nodes/05-detail.md](nodes/05-detail.md) | ✓ | 详细设计，业务流程细化 |
| 06 | GENHTML | [nodes/07-genHTML.md](nodes/07-genHTML.md) | ✓ | HTML原型生成，生成静态HTML原型 |
| 07 | CHANGE | [nodes/08-change.md](nodes/08-change.md) | - | 变更分析，增量变更处理 |

---

## 节点详情

### 01 - ROUTER (入口路由器)

| 属性 | 说明 |
|------|------|
| **触发条件** | Skill 被激活时 |
| **做什么** | 定义全局执行规范、提供节点概览表、检查 Memory.json 恢复状态 |
| **输出什么** | 节点概览表、流程锁定规则 |

```
[WHEN] Skill 被激活
[THEN] 
  1. 检查项目根目录是否存在 Memory.json
  2. 若存在 → 从 Memory.json 恢复状态，定位当前节点
  3. 若不存在 → 初始化新项目，意图识别
  4. 输出 AIPM 头部信息（当前节点、版本、状态）
[OUTPUT] 当前节点状态、节点概览表
```

---

### 02 - BRAINSTORM (头脑风暴)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户确认进入或从 ROUTER 恢复 |
| **做什么** | 基于需求方向进行功能策划，产出功能骨架（一级模块→页面/二级→子功能） |
| **输出什么** | 功能骨架 Markdown 文件 |

```
[WHEN] 用户确认进入头脑风暴 / 恢复到此节点
[THEN] 
  1. 发散提问：使用开放式问题引导思维（"最想做的是什么？"）
  2. 即时归纳：将收集的信息归纳为层级结构展示
  3. 循环确认：根据用户反馈调整或确认
  4. 整理产出：生成确认版功能骨架
  5. 等待用户确认
[OUTPUT] ./{项目名}_{datetime}/analysis_process/brainstorm.md
```

---

### 03 - CLARIFY (需求澄清)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户确认 BRAINSTORM 产出后 |
| **做什么** | 从 7 个维度深度挖掘需求：背景/痛点、业务目标、上下游系统、接口文档、业务规则、业务风险、埋点场景 |
| **输出什么** | 需求澄清 Markdown 文件 |

```
[WHEN] 用户确认 BRAINSTORM 后
[THEN] 
  1. 读取 BRAINSTORM 产出物
  2. 评估 7 个维度（哪些清晰、哪些缺失）
  3. 深度追问：至少 1 轮，最多 3 轮启发式追问
  4. 停止规则：业务背景清晰 或 已完成 3 轮
  5. 结构化复述需求
  6. 保存文件，等待用户确认
[OUTPUT] ./{项目名}_{datetime}/analysis_process/clarify.md

禁止：询问技术实现细节（数据库设计、接口规范、技术选型）
```

---

### 04 - ANALYSIS (需求分析)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户确认 CLARIFY 产出后 |
| **做什么** | 构建 PRD 骨架：项目基本信息、需求背景与目标、用户类型和端口、业务对象、业务流程图、页面列表、埋点方案、异常场景 |
| **输出什么** | 初步 PRD 骨架 Markdown 文件 |

```
[WHEN] 用户确认 CLARIFY 后
[THEN] 
  1. 读取 CLARIFY 产出物
  2. 深度填充：项目背景、目标表格、用户类型、业务对象表格
  3. 业务流程图：Mermaid 图表输出
  4. 页面列表：B/C 端页面流程 + 功能描述
  5. 埋点方案表格
  6. 异常场景处理
  7. 等待用户确认
[OUTPUT] ./{项目名}_{datetime}/output/V{version}/PRD_{version}.md
```

---

### 05 - DETAIL (详细设计)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户确认 ANALYSIS 产出后 |
| **做什么** | 细化业务流程、页面流程、数据点位和埋点方案，使用 Mermaid 绘制可视化流程图 |
| **输出什么** | 详细设计 Markdown 文件（按页面维度输出事件列表和异常处理） |

```
[WHEN] 用户确认 ANALYSIS 后
[THEN] 
  1. 读取 ANALYSIS 产出物
  2. 高层级业务流程图 + 页面流程图
  3. C端/B端页面和功能列表
  4. 埋点方案细化
  5. 按页面维度输出：
     - 事件编号：`/{pageNameInEN}/feat-{序号}`
     - 事件名称、绑定页面、触发节点、事件描述
     - 业务流程图 (Mermaid)
     - 事件逻辑、异常场景
  6. 等待用户确认
[OUTPUT] ./{项目名}_{datetime}/output/V{version}/PRD_{version}.md（追加到"详细设计"章节）
```

---

### 06 - GENHTML (HTML原型生成)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户确认 DETAIL 产出后 |
| **做什么** | 生成 ASCII 线框图 + HTML 高保真原型，使用 Mock 数据（优先欧莱雅兰蔻数据） |
| **输出什么** | 原型索引页面 + 各页面 HTML 原型文件 |

```
[WHEN] 用户确认 DETAIL 后
[THEN] 
  1. 读取 DETAIL 产出物
  2. 生成 Mock 数据（欧莱雅兰蔻数据优先）
  3. 生成 ASCII 线框图（实时打印到消息中）
  4. 逐个页面生成 HTML 原型
     - 如已安装 tdesign-component-helper Skill：调用技能
     - 如未安装：直接基于 TDesign 组件库生成
  5. 生成 prototype/index.html 原型索引
  6. 更新 PRD 文档"原型与交互"章节
  7. 等待用户确认
[OUTPUT] ./{项目名}_{datetime}/output/V{version}/prototype/
  - index.html (原型索引)
  - app/page_{page_name}.html (C端页面原型)
  - web/page_{page_name}.html (B端页面原型)
```

---

### 07 - CHANGE (变更分析)

| 属性 | 说明 |
|------|------|
| **触发条件** | 用户在任何节点提出"变更"、"加功能"、"改需求" |
| **做什么** | 评估变更影响，制定增量变更方案，避免整体回退 |
| **输出什么** | 变更分析报告 |

```
[WHEN] 用户说 "变更"、"加功能"、"改需求"
[THEN] 
  1. 分析变更内容
  2. 识别影响维度（页面、事件、数据结构）
  3. 评估影响范围
  4. 制定增量变更方案（精确到页面/事件粒度）
  5. 展示变更分析报告
  6. 确认后回退到受影响的主流程节点
[OUTPUT] 增量变更方案（不涉及节点文件，仅内存中处理）
```

---

## 内部能力：_manage

`nodes/_manage.md` 是内部共享能力，供其他节点调用。

### 状态管理

```
┌─────────────────────────────────────────┐
│ 节点状态机                               │
├─────────────────────────────────────────┤
│ DRAFT   - 默认状态、产出后、用户修改后   │
│ CONFIRM - 用户确认产出后                 │
│ PENDING - 未执行                         │
│ LOCKED  - 不能执行，前置节点未完成       │
│ UNLOCKED- 可以执行，前置节点已完成        │
│ COMPLETED - 已完成，等待流转             │
└─────────────────────────────────────────┘
```

**状态变更接口：**
- `get_current_node()` - 获取当前节点
- `get_node_status(node_name)` - 获取节点状态
- `update_node_status(node_name, status)` - 更新节点状态
- `transition_to_next_node()` - 流转到下一节点

### 记忆管理

```
关键原则：
1. 每次从文件读取 Memory.json，不依赖上下文缓存
2. 每次状态变更立即持久化
3. 节点完成后记录产出物路径 (file 字段)
```

**接口：**
- `read_memory()` - 读取 Memory.json
- `write_memory(data)` - 写入 Memory.json
- `update_node_output(node_name, file_path)` - 更新产出物路径
- `persist_memory()` - 持久化到文件
- `restore_memory()` - 从文件恢复

### 版本管理

```
版本号格式：V{major}.{minor}
递增规则：
- 用户修改后：minor +1 (V1.0 → V1.1)
- 用户确认后：不递增
- 需求变更 + 前序内容修改：major +1 (V1.x → V2.0)
```

---

## 用户反馈判定

| 用户说 | 判定 | 动作 |
|--------|------|------|
| "确认"、"没问题"、"继续" | CONFIRM | 进入下一节点 |
| "修改"、"不对"、"重来" | MODIFY | 重新执行当前节点 |
| "变更"、"加功能"、"改需求" | CHANGE | 进入变更分析 |

---

## 目录结构

```
product_manager/
├── SKILL.md                    # Skill 定义文件
├── legacy/
│   ├── README.md               # 历史材料说明
│   └── scripts/                # 旧 shell 工作流，仅保留作历史参考
├── nodes/
│   ├── README.md               # 节点目录说明
│   ├── _manage.md              # 内部共享能力（状态、记忆、版本）
│   ├── 01-router.md            # 入口路由器
│   ├── 02-brainstorm.md        # 头脑风暴
│   ├── 03-clarify.md           # 需求澄清
│   ├── 04-analysis.md          # 需求分析
│   ├── 05-detail.md             # 详细设计
│   ├── 07-genHTML.md           # HTML原型生成
│   └── 08-change.md            # 变更分析
├── references/
│   ├── prd_viewer_template.html    # PRD 查看器模板
│   ├── overview_template.html      # 概述页面模板
│   ├── page_doc_template.html     # 页面文档模板
│   └── loreal_mock_database.md    # Mock 数据（欧莱雅兰蔻）
├── assets/                     # 静态资源（图片等）
└── ...
```

说明：`legacy/scripts/` 不属于当前受支持的 skill 路径，仅作为旧实现的历史参考保留。

---

## 输出文件结构

### 不纳入版本管理（brainstorm目录）

```
./{项目名}_{datetime}/analysis_process/
│   ├── brainstorm.md            # 功能骨架
└── clarify.md                   # brainstorm+clarify整合内容
```

### 纳入版本管理（output目录）

```
./{项目名}_{datetime}/output/V{version}/
├── PRD_{version}.md           # PRD文档（整合所有节点产出）
└── prototype/
    ├── index.html              # 原型索引
    ├── app/
    │   └── page_*.html        # C端页面原型
    └── web/
        └── page_*.html        # B端页面原型
```

---

## 工作流图

```
START
  │
  ▼
CHECK{Memory.json?}
  │
  ├──NO──▶ 意图识别 + 方案确认 ──▶ 用户确认 ──▶ BRAINSTORM
  │
  └──YES──▶ 恢复状态 ──▶ 定位当前节点 ──▶ 输出 AIPM 头部
                                            │
  ┌─────────────────────────────────────────┘
  ▼
BRAINSTORM ──用户确认──▶ CLARIFY ──用户确认──▶ ANALYSIS
  │                                                  │
  │                                                  ▼
  │                                      DETAIL ──用户确认──▶ GENHTML
  │                                                       │
  ▼                                                       ▼
CHANGE ◀─────────────────────────────────────────────── DONE
  │
  │用户确认
  ▼
回退到受影响节点
```

---

## 核心原则

1. **节点线性执行**：严格按顺序执行，不可跳过
2. **用户确认**：所有节点完成后必须等待确认才能前进
3. **状态持久化**：每次状态变更立即写入 Memory.json
4. **版本管理**：历史版本永不覆盖，每次修改生成新版本
5. **从文件读取**：每次从 Memory.json 读取，不依赖上下文缓存
6. **禁止技术讨论**：CLARIFY 阶段只询问业务/功能问题，不问技术实现

---

## 触发条件

当用户进入产品需求分析全流程时触发，包括：
- 需求澄清
- 需求分析
- 详细需求设计
- 原型设计
- PRD 生成
- 需求变更场景
