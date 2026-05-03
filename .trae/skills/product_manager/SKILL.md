---
name: product_manager
description: 当用户正在进行产品经理工作，从产品需求分析、功能策划、澄清、详细设计、生成PRD和原型等节点时需要使用
---

# AIPM 产品经理工作流 Skill

> 从模糊需求到标准 PRD、Penpot 设计稿与 HTML 原型的完整链路。

---

## 概述

`product_manager` 是一套产品经理全流程工作流 Skill，用于帮助 AI Agent 完成从需求理解、PRD 产出、可视化设计到增量变更分析的标准流程。

**核心特点：**
- 9 节点标准流程：Router → Brainstorm → Clarify → Analysis → DrawUserStory → Detail → GenPrototype → GenHTML → Change
- 独立变更分析节点，支持增量回退
- 三级用户确认机制
- 完整状态持久化（`Memory.json`）
- 历史版本永久保留
- 目录分离：`analysis_process/`（不纳入版本管理）与 `output/{Version}/`（纳入版本管理）

---

## 节点概览

| 序号 | 节点 | 文件 | 用户确认 | 职责 |
|------|------|------|---------|------|
| 01 | ROUTER | `nodes/01-router.md` | - | 入口路由、状态恢复、知识卡片入口 |
| 02 | BRAINSTORM | `nodes/02-brainstorm.md` | ✓ | 头脑风暴，产出功能骨架 |
| 03 | CLARIFY | `nodes/03-clarify.md` | ✓ | 需求澄清，补齐边界与规则 |
| 04 | ANALYSIS | `nodes/04-analysis.md` | ✓ | 需求分析，生成初版 PRD |
| 05 | DRAWUSERSTORY | `nodes/05-drawUserStory.md` | ✓ | 在 Penpot 中绘制用户故事 |
| 06 | DETAIL | `nodes/06-detail.md` | ✓ | 页面级详细设计，细化事件、异常、埋点与状态机 |
| 07 | GENPROTOTYPE | `nodes/07-genPrototype.md` | ✓ | 在 Penpot 中绘制页面原型设计稿 |
| 08 | GENHTML | `nodes/08-genHTML.md` | ✓ | 生成 HTML 原型与索引页面 |
| 09 | CHANGE | `nodes/09-change.md` | ✓ | 变更分析与受影响节点回退 |

---

## 共享协议

### 状态机

`Memory.json` 中只保留三种持久状态：

- `PENDING`：节点尚未开始
- `DRAFT`：节点正在执行或等待修改
- `CONFIRM`：节点产出已获用户确认

### Memory.json

`nodes/_manage.md` 是唯一协议源，统一维护：

- `Memory.json` schema
- 节点状态流转
- 版本格式 `Vx.y`
- 节点输出结构 `node_list[].output`
- 全局 Penpot 页面索引 `penpot.pages`

文件型产物记录在 `node_list[].output.path`，Penpot 页面型产物记录在 `node_list[].output.page_key` 与 `node_list[].output.page_id`。

### 版本与路径

- 版本目录：`output/{Version}/`
- PRD 文件：`PRD_{Version}.md`
- 示例：`./{项目名}_{datetime}/output/V1.0/PRD_V1.0.md`

---

## 知识卡片

- Router 是唯一的知识卡片入口节点
- 每轮对话开始时，Router 必须先读取 `references/knowledge_cards/index.md`
- 再由 LLM 结合当前节点和用户输入决定需要加载哪些卡片
- 业务节点只消费 Router 提供的知识卡片上下文，不再定义独立加载策略

---

## Penpot 说明

- Penpot 通用 preflight 统一维护在 `skills/penpot-drawing-skill/SKILL.md`
- `drawUserStory`、`detail`、`genPrototype` 只引用该共享说明，不重复展开同一套 helper / MCP / layout 规则

---

## 输出文件结构

### 不纳入版本管理

```text
./{项目名}_{datetime}/analysis_process/
├── brainstorm.md
└── clarify.md
```

### 纳入版本管理

```text
./{项目名}_{datetime}/output/{Version}/
├── PRD_{Version}.md
└── prototype/
    ├── index.html
    ├── app/
    │   └── page_*.html
    └── web/
        └── page_*.html
```

---

## 工作流图

```text
START
  │
  ▼
CHECK{Memory.json?}
  │
  ├──NO──▶ Router ▶ Brainstorm
  │
  └──YES──▶ 恢复状态 ▶ 定位当前节点
                        │
                        ▼
Brainstorm → Clarify → Analysis → DrawUserStory → Detail → GenPrototype → GenHTML
    │
    └──────────────────────────────────────────────────────────────▶ Change
```

---

## 关键原则

1. 节点线性执行，不可跳过
2. 每个节点完成后必须等待用户确认
3. 所有状态变更立即写回 `Memory.json`
4. 每次对话都从 `Memory.json` 恢复状态，不依赖上下文缓存
5. CLARIFY 阶段只讨论业务与功能，不讨论技术实现