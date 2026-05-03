## Context

当前 `product_manager` skill 依赖多份节点文档协同完成 Router、需求分析、PRD、Penpot 与 HTML 原型流程，但这些文档对同一组内部契约存在重复定义和局部漂移。最明显的问题包括版本目录和 PRD 文件命名混用、Memory schema 不稳定、状态枚举不统一、知识卡片入口既写在 Router 又散落在其他文件、Penpot 前置说明在多个节点重复展开。由于这些节点依赖 `Memory.json` 和固定路径互相读取，这类漂移会直接影响流程恢复、节点切换和后续维护。

## Goals / Non-Goals

**Goals:**
- 为 `product_manager` 建立单一的内部协议：版本格式、Memory schema、状态枚举、节点输出结构和 Penpot 页面索引。
- 将知识卡片选择入口统一收敛到 Router，使业务节点只消费已加载上下文。
- 将 Penpot 通用前置说明收敛到 `penpot-drawing-skill`，减少节点文档重复。
- 收紧 `detail` 节点职责边界，使其专注页面级详细设计，而非重复公共规范。

**Non-Goals:**
- 不新增业务能力或新流程节点。
- 不改变主流程顺序：Router → Brainstorm → Clarify → Analysis → DrawUserStory → Detail → GenPrototype → GenHTML → Change。
- 不在本次变更中设计新的 Penpot 绘图规范或新的知识卡片内容。

## Decisions

### 1. `_manage.md` 作为唯一协议源
- 决定：由 `_manage.md` 单独维护 `Memory.json` schema、状态枚举、版本格式、节点流转和节点输出结构。
- 原因：这些内容属于跨节点共享契约，重复出现在 Router 和业务节点中时最容易漂移。
- 备选方案：继续在各节点写局部规则。
- 不采用原因：局部规则已经出现路径和状态不一致，继续分散维护只会扩大漂移。

### 2. 版本与文件命名完全标准化
- 决定：统一使用 `Vx.y` 作为版本格式，版本目录统一为 `output/{Version}/`，PRD 文件统一为 `PRD_{Version}.md`。
- 原因：当前 `V1`、`V1.0`、`PRD_V1.md`、`PRD_{version}.md` 并存，影响节点间文件读取与回写。
- 备选方案：仅更新部分节点，保留向后兼容示例。
- 不采用原因：这套 skill 仍处于内部整理阶段，保留双格式只会增加维护复杂度。

### 3. `Memory.json` 统一为节点输出索引模型
- 决定：节点产物统一记录在 `node_list[].output` 下，文件产物使用 `output.path`，Penpot 产物使用 `output.page_key` / `output.page_id`；`penpot.pages` 维护全局页面索引。
- 原因：当前有的节点写 `file`，有的节点写 `penpotPage`，扩展性差且语义不一致。
- 备选方案：继续沿用 `file` 和 `penpotPage` 等字段。
- 不采用原因：不同节点字段形态不同，会让读取方需要分支判断。

### 4. 状态机只保留三态
- 决定：持久态只允许 `PENDING`、`DRAFT`、`CONFIRM`。
- 原因：`LOCKED`、`UNLOCKED`、`COMPLETED` 更适合做推导态说明，不应和持久态并存。
- 备选方案：保留两层状态系统。
- 不采用原因：双状态系统已经与 `is_confirmed` 等表达混用，阅读和实现成本都高。

### 5. 知识卡片入口只在 Router
- 决定：Router 每轮先读取 `knowledge_cards/index.md`，再结合用户输入和当前节点由 LLM 自主选择知识卡片；业务节点不再定义加载策略。
- 原因：用户每轮对话都会先经过 Router，入口收敛后最容易保证一致性。
- 备选方案：保留 Router 默认表，同时允许节点补充加载。
- 不采用原因：这会继续制造第二套规则源。

### 6. Penpot 通用前置说明收敛到 `penpot-drawing-skill`
- 决定：通用的 Penpot preflight、helper 依赖、MCP 关键规则只在 `penpot-drawing-skill` 中维护；`drawUserStory`、`detail`、`genPrototype` 只保留简短引用。
- 原因：当前同一段说明在多个节点重复出现，变更成本高且容易不同步。
- 备选方案：每个节点维护自己的一份完整 Penpot 前置说明。
- 不采用原因：重复率高，已出现编号和内容维护负担。

### 7. `detail` 节点保留页面级详细设计职责
- 决定：`detail` 负责页面结构、事件、异常处理、埋点时机、必要状态机，不再承担大段公共规范描述。
- 原因：这能保留它作为 PRD 细化节点的价值，同时减少与绘制型节点的职责重叠。
- 备选方案：把 `detail` 拆成两个节点。
- 不采用原因：本次目标是收敛契约，不扩展流程复杂度。

## Risks / Trade-offs

- [旧文档示例仍残留双格式路径] → 统一检索并替换所有版本、路径、状态示例。
- [节点对新 Memory schema 的引用不完整] → 逐节点核对读取字段和回写字段，确保都改为 `node_list[].output`。
- [Router 收敛后，业务节点仍保留旧知识卡片规则] → 删除节点内重复加载说明，只保留“接收 Router 上下文”。
- [Penpot 说明过度收敛导致节点上下文不够] → 在 `penpot-drawing-skill` 中保留完整 preflight 清单，并在节点中明确引用入口。
- [detail 职责缩减后与 genPrototype 边界仍模糊] → 在节点文档中明确 detail 产出 PRD 细化内容，genPrototype 仅消费 PRD 并绘制设计稿。
