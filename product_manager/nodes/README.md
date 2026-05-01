# Nodes Directory

AIPM Skill 节点文件目录

## 文件命名规范

线性节点文件命名：`{序号}-{node_name}.md`
内部共享能力文件命名：`_manage.md`（下划线前缀表示内部使用）

示例：
- `01-router.md`
- `02-brainstorm.md`
- `_manage.md`（内部能力，供其他节点调用）

## 节点概览

| 序号 | 节点 | 用户确认 | 说明 |
|------|------|---------|------|
| 01 | router | - | 入口，无状态管理 |
| 02 | brainstorm | ✓ | 头脑风暴 |
| 03 | clarify | ✓ | 需求澄清(7维度) |
| 04 | analysis | ✓ | 需求分析(PRD骨架) |
| 05 | drawUserStory | ✓ | 用户故事可视化(Penpot) |
| 06 | detail | ✓ | 详细设计(流程图) |
| 07 | genPrototype | ✓ | 页面原型设计(Penpot) |
| 08 | genHTML | ✓ | HTML原型生成 |
| 09 | change | ✓ | 变更分析(可选) |

## 内部共享能力

### _manage.md

`product_manager/nodes/_manage.md` 是内部共享能力，供其他节点调用，不作为独立节点执行。

**职责：**
- 状态管理：节点状态机（DRAFT/CONFIRM）、节点流转、需求变更路由
- 记忆管理：上下文快照读写、Memory.json 持久化
- 版本管理：版本号查询和递增

**调用时机：**
在以下节点完成后自动调用（无需用户确认）：
- brainstorm ✓ → manage.update_node_status + manage.transition_to_next_node
- clarify ✓ → manage.update_node_status + manage.transition_to_next_node
- analysis ✓ → manage.update_node_status + manage.transition_to_next_node
- drawUserStory ✓ → manage.update_node_status + manage.transition_to_next_node
- detail ✓ → manage.update_node_status + manage.transition_to_next_node
- genPrototype ✓ → manage.update_node_status + manage.transition_to_next_node
- genHTML ✓ → manage.update_node_status + manage.transition_to_next_node
- change ✓ → manage.update_node_status + manage.transition_to_first_affected_node
✓ = 用户已确认
