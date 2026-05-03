---
name: _manage
description: 节点状态管理和流程控制 - 负责节点状态流转、Memory.json 读写和版本控制
---

# _manage - 节点状态管理和流程控制

> **核心功能**：作为 `product_manager` 工作流的唯一协议源，维护 `Memory.json` schema、节点状态流转、版本格式和节点输出结构。

---

## 步骤1 核心职责

`_manage` 只负责三类共享协议：

1. 节点状态管理 - 跟踪和更新各个节点的持久状态
2. Memory.json 读写 - 维护项目状态、节点输出索引和 Penpot 页面索引
3. 版本控制 - 维护统一的版本格式与版本目录

---

## 步骤2 节点状态管理

### 2.1 持久状态定义

| 状态 | 含义 |
|------|------|
| `PENDING` | 节点尚未开始，等待前置节点完成 |
| `DRAFT` | 当前节点正在执行或等待用户修改 |
| `CONFIRM` | 当前节点产出已获用户确认 |

> `LOCKED`、`UNLOCKED`、`COMPLETED` 仅可作为解释流程时的口头描述，不进入 `Memory.json` 持久化状态。

### 2.2 API 函数

#### get_current_node()
获取当前正在执行的节点名称。

#### get_node_status(node_name)
获取指定节点的当前状态。

#### update_node_status(node_name, status)
更新指定节点的状态为 `PENDING`、`DRAFT` 或 `CONFIRM`。

#### transition_to_next_node()
将当前节点标记为完成，并自动流转到下一个节点：

1. 检查当前节点是否已标记为 `CONFIRM`
2. 在节点列表中找到下一个节点
3. 将下一个节点状态设置为 `DRAFT`
4. 更新 `current_node` 为下一个节点

#### transition_to_first_affected_node(affected_nodes)
当上游节点变更时，重置受影响的节点到第一个受影响的节点：

1. 受影响节点列表示例：`["clarify", "analysis", "detail"]`
2. 将所有受影响节点的状态重置为 `DRAFT`
3. 清除受影响节点的 `output` 字段
4. 将 `current_node` 设置为受影响列表中的第一个节点
5. 调用 `persist_memory()` 持久化状态

### 2.3 标准节点流转顺序

1. `router` → `brainstorm`
2. `brainstorm` → `clarify`
3. `clarify` → `analysis`
4. `analysis` → `drawUserStory`
5. `drawUserStory` → `detail`
6. `detail` → `genPrototype`
7. `genPrototype` → `genHTML`
8. `genHTML` → `change`（如无变更则项目完成）

---

## 步骤3 Memory.json 协议

### 3.1 结构说明

`Memory.json` 是整个项目的状态中心，必须使用统一结构记录项目状态、节点输出和 Penpot 页面索引。

```json
{
  "project": {
    "name": "项目名称",
    "version": "V1.0",
    "directory": "项目名_YYYYMMDD_HHMMSS"
  },
  "current_node": "detail",
  "directories": {
    "analysis_process": "项目名_YYYYMMDD_HHMMSS/analysis_process",
    "output": "项目名_YYYYMMDD_HHMMSS/output/V1.0"
  },
  "penpot": {
    "projectUrl": "https://design.penpot.app/#/workspace?team-id=...&file-id=...",
    "fileId": "file-uuid",
    "pages": {
      "userStory": "page-user-story-uuid",
      "prd": "page-prd-uuid",
      "prototype": "page-prototype-uuid"
    }
  },
  "node_list": [
    {
      "name": "router",
      "status": "CONFIRM"
    },
    {
      "name": "brainstorm",
      "status": "CONFIRM",
      "output": {
        "type": "markdown",
        "path": "项目名_YYYYMMDD_HHMMSS/analysis_process/brainstorm.md"
      }
    },
    {
      "name": "clarify",
      "status": "CONFIRM",
      "output": {
        "type": "markdown",
        "path": "项目名_YYYYMMDD_HHMMSS/analysis_process/clarify.md"
      }
    },
    {
      "name": "analysis",
      "status": "CONFIRM",
      "output": {
        "type": "prd",
        "path": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"
      }
    },
    {
      "name": "drawUserStory",
      "status": "CONFIRM",
      "output": {
        "type": "penpot_page",
        "page_key": "userStory",
        "page_id": "page-user-story-uuid"
      }
    },
    {
      "name": "detail",
      "status": "DRAFT",
      "output": {
        "type": "prd",
        "path": "项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md"
      }
    },
    {
      "name": "genPrototype",
      "status": "PENDING",
      "output": {
        "type": "penpot_page",
        "page_key": "prototype",
        "page_id": ""
      }
    },
    {
      "name": "genHTML",
      "status": "PENDING",
      "output": {
        "type": "html_bundle",
        "path": "项目名_YYYYMMDD_HHMMSS/output/V1.0/prototype/"
      }
    },
    {
      "name": "change",
      "status": "PENDING"
    }
  ]
}
```

### 3.2 字段约束

- `project.version`：统一使用 `Vx.y` 格式，如 `V1.0`
- `directories.output`：指向当前版本目录，如 `项目名_YYYYMMDD_HHMMSS/output/V1.0`
- `penpot.pages`：全局维护 Penpot 页面索引，键名由工作流统一约定
- `node_list[].output.path`：用于 markdown、PRD、HTML bundle 等文件型产物
- `node_list[].output.page_key` / `node_list[].output.page_id`：用于 Penpot 页面型产物

### 3.3 API 函数

#### get_project_directory()
获取当前项目目录名称，格式为 `项目名_YYYYMMDD_HHMMSS`。

#### read_memory()
从 `Memory.json` 读取项目信息，返回 JSON 对象。

#### write_memory(data)
将数据写入 `Memory.json`。

#### update_node_output(node_name, output)
更新指定节点的 `output` 字段。文件型产物传入 `{"type": ..., "path": ...}`，Penpot 型产物传入 `{"type": "penpot_page", "page_key": ..., "page_id": ...}`。

#### persist_memory()
将当前内存状态保存到 `Memory.json`。

#### restore_memory()
从 `Memory.json` 恢复节点状态。

---

## 步骤4 版本控制

### 4.1 版本格式

版本号格式统一为 `V{major}.{minor}`，如 `V1.0`、`V1.1`。

### 4.2 版本目录与文件命名

- 版本目录：`output/{Version}/`
- PRD 文件：`PRD_{Version}.md`

示例：

```text
项目名_YYYYMMDD_HHMMSS/output/V1.0/PRD_V1.0.md
```

### 4.3 API 函数

#### get_current_version()
获取当前项目版本号。

#### increment_version(change_type)
递增项目版本号：

- `minor`：新增或局部修改，示例 `V1.0 → V1.1`
- `major`：需求变更导致前序内容回退，示例 `V1.x → V2.0`

---

## 步骤5 节点完成后的标准流程

```markdown
## 节点完成后的标准流程
1. 将生成的内容保存到文件或 Penpot 页面
2. 调用 manage.update_node_output(...) 更新节点 output
3. 调用 manage.update_node_status("节点名称", "CONFIRM")
4. 调用 manage.transition_to_next_node()
5. 调用 manage.persist_memory()
6. 告知用户已完成，并进入下一个节点
```

---

## 步骤6 注意事项

- 节点状态管理必须严格按照既定顺序执行，不能跳过节点
- 每次对话都必须以 `Memory.json` 为唯一状态来源，不依赖上下文缓存
- 节点完成后必须同步更新 `node_list[].output`
- Router 是唯一的知识卡片入口，`_manage` 不定义知识卡片加载策略
