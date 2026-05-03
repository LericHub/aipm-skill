## Why

`product_manager` 这套 skill 的主流程已经成型，但节点文档之间存在协议漂移：版本目录与 PRD 文件命名不一致、状态定义混用多套枚举、知识卡片加载规则分散重复、Penpot 前置说明在多个节点重复维护。这些问题会让多轮对话恢复、节点流转和后续维护变得不稳定，已经到了需要统一收敛的时候。

## What Changes

- 统一 `product_manager` 工作流的版本格式、目录结构和 PRD 文件命名规则，固定为 `PRD_{Version}.md` 与 `output/{Version}/`。
- 重构 `Memory.json` 契约，统一节点输出结构为 `node_list[].output`，并将 `penpot.pages` 作为全局页面索引。
- 统一节点状态机，只保留 `PENDING`、`DRAFT`、`CONFIRM` 三种状态，并移除其他冗余状态表达。
- 将 `_manage.md` 收敛为 Memory schema、状态流转、版本规则的唯一协议源，其他节点仅消费该协议。
- 将知识卡片规则收敛到 Router：每轮对话先读取 `knowledge_cards/index.md`，再由 LLM 根据当前节点和用户输入选择使用哪些卡片。
- 将 Penpot 通用前置说明收敛到 `penpot-drawing-skill`，`drawUserStory`、`detail`、`genPrototype` 节点只保留引用。
- 瘦身 `detail` 节点职责，保留页面结构、事件、异常、埋点、状态机等页面级详细设计内容，移除重复的公共规范说明。

## Capabilities

### New Capabilities
- `product-manager-node-contracts`: 规范产品经理工作流节点之间的版本、状态、Memory、知识卡片入口和 Penpot 引用契约。

### Modified Capabilities

## Impact

- 受影响目录：`product_manager/SKILL.md`、`product_manager/nodes/`、`product_manager/references/knowledge_cards/index.md`、`product_manager/skills/penpot-drawing-skill/`。
- 受影响系统：Router 路由规则、`_manage` Memory 协议、节点间文件读取路径、Penpot 页面索引方式。
- 不涉及业务功能新增，但会改变这套 skill 的内部文档契约与维护方式。
