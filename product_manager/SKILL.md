---
name: product_manager
description: 产品经理全流程工作流，从模糊需求到标准PRD完整链路；当用户进入需求分析、原型设计、PRD生成等场景时触发。
---

# 产品经理工作流

## 核心能力

- 9节点标准流程：ROUTER → BRAINSTORM → CLARIFY → ANALYSIS → DETAIL → PROTOTYPING → WRITING
- 独立变更分析节点（CHANGE）- 增量变更无需整体回退
- 三级用户确认机制
- 完整状态持久化（Memory.json）
- 历史版本永久保留

---

## 工作流图

```
START
  │
  ▼
CHECK{Memory.json?}
  │
  ├──NO──▶ 意图识别 + 方案确认 ──▶ 用户确认 ──▶ CLARIFY
  │
  └──YES──▶ 恢复状态 ──▶ 定位当前节点
                                    │
CLARIFY ──用户确认──▶ ANALYSIS ──用户确认──▶ DETAIL
  │                                         │
  │                                         ▼
  │                             PROTOTYPING ──用户确认──▶ WRITING
  │                                             │
  ▼                                             ▼
CHANGE ◀──────────────────────────────────────── DONE
  │
  │用户确认
  ▼
回退到受影响节点
```

---

## 节点映射表

- **01 ROUTER** (`nodes/01-router.md`)  
  入口，状态恢复/初始化 → 下一节点：BRAINSTORM

- **02 BRAINSTORM** (`nodes/02-brainstorm.md`)  
  头脑风暴，功能骨架发散 → 下一节点：CLARIFY → 用户确认

- **03 CLARIFY** (`nodes/03-clarify.md`)  
  需求澄清，7维度深度挖掘 → 下一节点：ANALYSIS → 用户确认

- **04 ANALYSIS** (`nodes/04-analysis.md`)  
  需求分析，PRD骨架构建 → 下一节点：DETAIL → 用户确认

- **05 DETAIL** (`nodes/05-detail.md`)  
  详细设计，业务流程图 → 下一节点：PROTOTYPING → 用户确认

- **06 PROTOTYPING** (`nodes/06-prototyping.md`)  
  原型制作，HTML原型 → 下一节点：WRITING → 用户确认

- **07 WRITING** (`nodes/07-writing.md`)  
  PRD撰写，PRD查看器 → 完成 → 用户确认

- **08 CHANGE** (`nodes/08-change.md`)  
  变更分析，增量变更 → 回退到受影响节点

---

## 强制执行规则

**严格遵守以下规则：**

1. **节点线性执行**：必须按顺序执行所有节点，不可跳过、不可遗漏、不可随意跳转
2. **用户确认机制**：每个节点完成后必须等待用户确认才能进入下一节点
   - 用户说"确认"、"没问题"、"继续" → 进入下一节点
   - 用户说"修改"、"不对" → 重新执行当前节点
   - 用户说"变更"、"加功能" → 进入 CHANGE 节点
3. **状态持久化**：每次状态变更立即写入 Memory.json，不依赖上下文缓存
4. **版本管理**：
   - 用户修改：minor+1 (V1.0 → V1.1)
   - 需求变更：major+1 (V1.x → V2.0)
5. **从文件读取**：每次从 Memory.json 读取状态，不依赖上下文记忆
6. **禁止技术讨论**：CLARIFY 阶段只询问业务/功能问题，不讨论技术实现

---

## 内部能力：_manage

`nodes/_manage.md` 是内部共享能力，供其他节点调用。

**状态管理接口：**
- `get_current_node()` - 获取当前节点
- `get_node_status(node_name)` - 获取节点状态
- `update_node_status(node_name, status)` - 更新节点状态
- `transition_to_next_node()` - 流转到下一节点

**记忆管理接口：**
- `read_memory()` - 读取 Memory.json
- `write_memory(data)` - 写入 Memory.json
- `persist_memory()` - 持久化到文件
- `restore_memory()` - 从文件恢复

---

## 输出文件结构

- `output/V{version}/brainstorm/brainstorm.md` - 功能骨架
- `output/V{version}/clarify/clarify.md` - 需求澄清
- `output/V{version}/analysis/analysis.md` - PRD骨架
- `output/V{version}/detail/detail.md` - 详细设计
- `output/V{version}/prototyping/proto_index.html` - 原型索引
- `output/V{version}/prototyping/page_*.html` - 页面原型
- `output/V{version}/writing/index.html` - PRD查看器
- `output/V{version}/writing/doc/*.html` - 页面文档

---

## 资源索引

- **节点文件**：`product_manager/nodes/01-router.md` - `product_manager/nodes/08-change.md`
- **内部能力**：`product_manager/nodes/_manage.md`
- **Mock数据**：`product_manager/references/loreal_mock_database.md`（欧莱雅兰蔻数据）
- **模板文件**：`product_manager/references/*.html`