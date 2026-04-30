---
name: 01-router
description: 入口路由器，定义全局执行规范和节点概览
---

# 01 - Router (入口路由器)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容，作为本节点后续输出的参考基础

## 步骤2 职责
- 定义全局执行规范
- 提供节点概览表
- 引导 LLM 加载技能文件
- 流程锁定与强制执行

---

## 步骤3 执行规范

1. **节点线性执行**: router → brainstorm → clarify → analysis → detail → prototyping → writing → change
2. **用户确认**: 所有节点完成后需确认
3. **状态管理**: 由 `_manage` 内部能力统一处理（状态更新、快照持久化、版本管理）
4. **目录初始化**: 在 brainstorm 节点首次需要写文件时创建（第一个需要写文件的节点）

---

## 步骤4 流程强制锁机制

### 节点锁状态

| 状态 | 说明 |
|------|------|
| `LOCKED` | 不能执行，前置节点未完成 |
| `UNLOCKED` | 可以执行，前置节点已完成 |
| `COMPLETED` | 已完成，等待流转 |

### 节点流转锁规则

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
2. Memory.json 中对应节点 is_confirmed = true
3. CURRENT_SNAPSHOT_{NODE} 已写入
4. 已输出完整的节点产出物

### 用户反馈判定

| 用户说 | 判定 | 动作 |
|--------|------|------|
| "确认"、"没问题"、"继续" | CONFIRM | 进入下一节点 |
| "修改"、"不对"、"重来" | MODIFY | 重新执行当前节点 |
| "变更"、"加功能"、"改需求" | CHANGE | 进入变更分析 |

### CHANGE 节点定位

- CHANGE 是可选节点，不参与主流程计数
- 仅在用户提出"变更"时才进入
- 处理完后回退到受影响的主流程节点

---

## 步骤5 文件生成规则

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
output/V{version}/
├── brainstorm/brainstorm.md
├── clarify/clarify.md
├── analysis/analysis.md
├── detail/detail.md
├── prototyping/
│   ├── proto_index.html
│   └── page_{page_name}.html
└── writing/
    ├── index.html
    └── overview.html
```

---

## 步骤6 内部能力

- `_manage`: 状态管理、记忆持久化、版本管理（see ./_manage.md）

---

## 步骤7 节点概览

| 序号 | 节点 | 用户确认 | 说明 |
|------|------|---------|------|
| 01 | router | - | 入口，无状态管理 |
| 02 | brainstorm | ✓ | 头脑风暴 |
| 03 | clarify | ✓ | 需求澄清(7维度) |
| 04 | analysis | ✓ | 需求分析(PRD骨架) |
| 05 | detail | ✓ | 详细设计(流程图) |
| 06 | prototyping | ✓ | 原型制作 |
| 07 | writing | ✓ | PRD撰写 |
| 08 | change | ✓ | 变更分析(可选) |

---

## 步骤8 节点详情（简短引用）

- brainstorm: see ./02-brainstorm.md
- clarify: see ./03-clarify.md
- analysis: see ./04-analysis.md
- detail: see ./05-detail.md
- prototyping: see ./06-prototyping.md
- writing: see ./07-writing.md
- change: see ./08-change.md
