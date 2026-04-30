---
name: 08-change
description: 变更分析技能。针对已进入开发阶段的需求进行变更影响评估，输出页面/事件粒度的增量变更方案，避免整体回退。适用于任何阶段的需求变更请求。
---

# 08 - Change (变更分析)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容，作为本节点后续输出的参考基础

## 步骤2 职责
- 评估变更影响，制定回退策略
- 变更识别维度
- 影响范围评估
- 增量变更模式
- 变更分析报告格式

---

## 步骤3 加载指引

### 前序节点产出物

在进入 change 节点之前，需要先分析用户变更内容，判断受影响的前序节点：

1. **接收用户变更内容**：用户描述想修改什么
2. **LLM 分析影响范围**：判断涉及哪些节点
3. **调用 manage 获取受影响节点**：根据 LLM 分析结果，回退到第一个受影响的节点

### 变化节点判断参考

| 变更涉及 | 影响节点 |
|----------|---------|
| 需求方向/目标 | brainstorm 及后续 |
| 业务规则/流程 | clarify 及后续 |
| 页面/功能/数据结构 | analysis 及后续 |
| 详细设计/埋点方案 | detail 及后续 |
| 原型样式/交互 | prototyping 及后续 |
| PRD 文档错误 | writing 单独 |

### 执行流程

1. 接收用户变更内容
2. LLM 分析变更涉及哪些维度
3. 判断需要回退的节点列表
4. 调用 manage.transition_to_first_affected_node(节点列表)
5. 回退到第一个受影响的节点重新执行

---

## 步骤4 完成后

1. 展示产出摘要
2. 等待用户确认
3. 确认后 → 打包输出目录：`zip -r output/{project_name}_V{version}_output_{YYYYMMDD_HHmm}.zip output/V{version}/`
4. 确认后 → 调用 manage.update_node_status("change", "CONFIRM")
5. 确认后 → 调用 manage.transition_to_first_affected_node(受影响节点列表)
6. 确认后 → 调用 manage.persist_memory()
7. 回退到第一个受影响的节点
