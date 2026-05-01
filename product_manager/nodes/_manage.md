---
name: _manage
description: 节点状态管理和流程控制 - 负责节点状态流转、Memory.json 读写和版本控制
---

# _manage - 节点状态管理和流程控制

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 理解index中记录的每个知识卡片的内容范围、文件位置和激活场景规则
4. 基于匹配到的知识卡片内容完成本节点后续的流程控制工作

> **核心功能**：管理各个节点的状态流转，持久化项目信息到 Memory.json，以及版本控制，确保整个产品需求流程的有序推进。

---

## 步骤2 核心职责

manage 节点主要管理以下三个方面：
1. 节点状态管理 - 跟踪和更新各个节点的状态，确保节点按正确顺序流转
2. Memory.json 读写 - 读取和写入项目信息，包括节点列表、状态和输出文件
3. 版本控制 - 管理项目的版本号，支持版本递增

---

## 步骤3 节点状态管理

### 1.1 节点状态定义

| 状态 | 含义 |
|------|------|
| `DRAFT` | 节点正在编辑中，尚未完成 |
| `CONFIRM` | 节点已完成，用户确认通过 |
| `PENDING` | 节点尚未开始，等待前面的节点完成 |

### 1.2 API 函数

#### get_current_node()
获取当前正在执行的节点名称。

#### get_node_status(node_name)
获取指定节点的当前状态。

#### update_node_status(node_name, status)
更新指定节点的状态为 DRAFT 或 CONFIRM。

#### transition_to_next_node()
将当前节点标记为完成，并自动流转到下一个节点：
1. 检查当前节点是否已标记为 CONFIRM
2. 在节点列表中找到下一个节点
3. 将下一个节点状态设置为 DRAFT
4. 更新 current_node 为下一个节点

#### transition_to_first_affected_node(affected_nodes)
当上游节点变更时，重置受影响的节点到第一个受影响的节点：
1. 受影响节点列表通常为 ["clarify", "analysis", "detail"]
2. 将所有受影响节点的状态重置为 DRAFT
3. 清除受影响节点的输出文件路径
4. 将 current_node 设置为受影响列表中的第一个节点
5. 调用 persist_memory() 持久化状态

### 1.3 节点状态流转的标准流程

标准节点流转顺序为：
1. router → brainstorm
2. brainstorm → clarify
3. clarify → analysis
4. analysis → drawUserStory
5. drawUserStory → detail
6. detail → genPrototype
7. genPrototype → genHTML
8. genHTML → change（如无变更则项目完成）

---

## 步骤4 Memory.json 读写

### 2.1 Memory.json 结构

Memory.json 是整个项目的状态中心，包含项目基本信息、当前节点和节点列表及其状态。

完整的 Memory.json 结构示例：
```json
{
  "project": {
    "name": "项目名称",
    "version": "V1.0",
    "directory": "项目名_YYYYMMDD_HHMMSS"
  },
  "current_node": "detail",
  "directories": {
    "brainstorm": "项目名_YYYYMMDD_HHMMSS/brainstorm",
    "output": "项目名_YYYYMMDD_HHMMSS/output"
  },
  "penpot": {
    "projectUrl": "https://design.penpot.app/#/workspace?team-id=...&file-id=...",
    "fileId": "",
    "pages": {
      "userStory": "",
      "prototype": "",
      "prd": ""
    }
  },
  "node_list": [
    {"name": "router", "status": "CONFIRM"},
    {"name": "brainstorm", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/brainstorm.md"},
    {"name": "clarify", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/analysis.md"},
    {"name": "analysis", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "drawUserStory", "status": "PENDING", "penpotPage": "userStory"},
    {"name": "detail", "status": "DRAFT", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "genPrototype", "status": "PENDING", "penpotPage": "prototype"},
    {"name": "genHTML", "status": "PENDING", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/prototype/"},
    {"name": "change", "status": "PENDING"}
  ]
}
```

**说明**：
- `directories.brainstorm`：不纳入版本管理的brainstorm目录路径
- `directories.output`：纳入版本管理的output目录路径
- `penpot`：PenPot项目信息，包含项目URL、文件ID和页面ID
- `node_list[].penpotPage`：节点关联的PenPot页面标识（userStory/prototype/prd）
- brainstorm和clarify节点的file指向brainstorm目录
- analysis~genHTML节点的file指向output目录

### 2.2 API 函数

#### get_project_directory()
获取当前项目的目录名称，格式为 `项目名_YYYYMMDD_HHMMSS`。

#### read_memory()
从 Memory.json 文件读取项目信息，返回 JSON 对象。

Memory.json 示例内容：
```json
{
  "project": {
    "name": "项目名称",
    "version": "V1.0",
    "directory": "项目名_YYYYMMDD_HHMMSS"
  },
  "current_node": "detail",
  "directories": {
    "brainstorm": "项目名_YYYYMMDD_HHMMSS/brainstorm",
    "output": "项目名_YYYYMMDD_HHMMSS/output"
  },
  "penpot": {
    "projectUrl": "https://design.penpot.app/#/workspace?team-id=...&file-id=...",
    "fileId": "",
    "pages": {
      "userStory": "",
      "prototype": "",
      "prd": ""
    }
  },
  "node_list": [
    {"name": "router", "status": "CONFIRM"},
    {"name": "brainstorm", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/brainstorm.md"},
    {"name": "clarify", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/analysis.md"},
    {"name": "analysis", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "drawUserStory", "status": "PENDING", "penpotPage": "userStory"},
    {"name": "detail", "status": "DRAFT", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "genPrototype", "status": "PENDING", "penpotPage": "prototype"},
    {"name": "genHTML", "status": "PENDING", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/prototype/"},
    {"name": "change", "status": "PENDING"}
  ]
}
```

#### write_memory(data)
将数据写入 Memory.json 文件。

#### update_node_output(node_name, file_path)
更新指定节点的输出文件路径到 node_list 中对应的 file 字段。

#### persist_memory()
将当前的内存状态保存到 Memory.json 文件。

#### restore_memory()
从 Memory.json 恢复节点状态。

---

## 步骤5 版本控制

### 3.1 版本号格式

版本号格式为 `V{major}.{minor}`，如 V1.0, V1.1 等。

### 3.2 API 函数

#### get_current_version()
获取当前项目的版本号。

#### increment_version()
递增项目版本号：
- 如果是节点完成确认，递增 minor 版本（V1.0 → V1.1）
- 如果是流程重新开始，重置版本

---

## 步骤6 节点完成后的标准流程

所有节点完成后，都需要调用 "节点完成后的标准流程" 中的 manage 函数：

```markdown
## 节点完成后的标准流程
1. 将生成的内容保存到文件
2. 更新节点输出路径
3. 更新节点状态为 CONFIRM
4. 调用 manage.update_node_status("节点名称", "CONFIRM")
5. 调用 manage.transition_to_next_node()
6. 调用 manage.persist_memory()
7. 告知用户已完成，并进入下一个节点
```

### 4.1 保存输出文件并更新 Memory.json

保存文件后，需要按以下步骤更新 Memory.json：

```markdown
## 节点完成后的标准流程
1. 将生成的内容保存到文件，如 {项目名}_{datetime}/output/V{version}/PRD_V{version}.md
2. 调用 manage.update_node_output("detail", "{项目名}_{datetime}/output/V{version}/PRD_V{version}.md")
3. ... 其他节点完成确认流程
```

---

## 步骤7 Memory.json 完整结构

```json
{
  "project": {
    "name": "项目名称",
    "version": "V1.0",
    "directory": "项目名_YYYYMMDD_HHMMSS"
  },
  "current_node": "detail",
  "directories": {
    "brainstorm": "项目名_YYYYMMDD_HHMMSS/brainstorm",
    "output": "项目名_YYYYMMDD_HHMMSS/output"
  },
  "penpot": {
    "projectUrl": "https://design.penpot.app/#/workspace?team-id=...&file-id=...",
    "fileId": "",
    "pages": {
      "userStory": "",
      "prototype": "",
      "prd": ""
    }
  },
  "node_list": [
    {"name": "router", "status": "CONFIRM"},
    {"name": "brainstorm", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/brainstorm.md"},
    {"name": "clarify", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/brainstorm/analysis.md"},
    {"name": "analysis", "status": "CONFIRM", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "drawUserStory", "status": "PENDING", "penpotPage": "userStory"},
    {"name": "detail", "status": "DRAFT", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"},
    {"name": "genPrototype", "status": "PENDING", "penpotPage": "prototype"},
    {"name": "genHTML", "status": "PENDING", "file": "项目名_YYYYMMDD_HHMMSS/output/V1.0/prototype/"},
    {"name": "change", "status": "PENDING"}
  ]
}
```

---

## 步骤8 知识卡片系统

### 6.1 核心说明
所有知识卡片相关的配置**全部收敛到 `product_manager/references/knowledge_cards/index.md` 作为唯一数据源**，本文件不硬编码任何卡片相关规则，所有逻辑均通过读取索引文件动态获取。

### 6.2 基础路径配置
```
# 知识卡片根路径（与index.md中基础配置保持一致）
KNOWLEDGE_CARDS_PATH = "product_manager/references/knowledge_cards"
# 索引文件唯一路径
KNOWLEDGE_CARD_INDEX = "product_manager/references/knowledge_cards/index.md"
```

### 6.3 配置读取规则
1. 卡片元数据（名称、适用节点、激活场景、文件路径等）：全部读取 index.md 的「卡片目录」章节
2. 使用规则：全部读取 index.md 的「使用规则」章节
3. 加载策略：全部读取 index.md 的「加载策略」章节
4. 各节点加载规则：全部读取 index.md 的「各节点加载规则」章节

---

## 步骤9 知识卡片API

API实现逻辑完全遵循 index.md 的「API调用规范」：

### 7.1 load_relevant_knowledge_cards(user_input, current_node)
根据用户输入和当前节点，智能加载相关的知识卡片内容：
- 输入参数：`user_input`（用户需求文本）、`current_node`（当前节点名称）
- 返回值：匹配到的所有知识卡片内容列表
- 实现逻辑：严格按照 index.md 中的「加载策略」执行匹配

### 7.2 get_knowledge_card(card_name)
获取指定名称的知识卡片完整内容：
- 输入参数：`card_name`（卡片中文名称，与index.md卡片目录中的名称完全匹配）
- 返回值：对应卡片的完整markdown内容

---

## 步骤10 注意事项

- 节点状态管理要严格按照节点顺序进行，不能跳过节点
- Memory.json 需要及时持久化，避免状态丢失
- 版本控制要合理递增，每次确认完成后更新版本号
- 知识卡片加载要根据节点类型和用户需求进行，避免加载不相关的知识卡片
