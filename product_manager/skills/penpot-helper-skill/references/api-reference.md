# Penpot API 实测参考

> v2.15 实地验证结果。后端类型定义来自 `https://design.penpot.app/api/main/doc`。

---

## 关键规则

1. **所有 API 统一走 `/api/rpc/command/<name>`** — 不存在 `/api/rpc/query/` 路径
2. **必须携带 `Accept: application/json`** — 默认返回 `application/transit+json` 格式（不可读）
3. **页面管理不是独立 API** — `add_page` / `rename_page` / `delete_page` / `list_pages` 在 v2.15 中不存在。页面增删改通过 MCP Plugin 的 change 机制执行，页面列表通过 `get-file` 响应中的 `data.pages` + `data.pagesIndex` 获取

---

## 可用 API 清单

### 用户

| API | 路径 | 参数 | 说明 |
|-----|------|------|------|
| `get-profile` | `POST /api/rpc/command/get-profile` | `{}` | 返回用户信息、`defaultProjectId`、`defaultTeamId` |

### 文件管理

| API | 路径 | 参数 | 说明 |
|-----|------|------|------|
| `create-file` | `POST /api/rpc/command/create-file` | `name`, `project-id` | 创建文件，返回 fileId 和默认 Page 1 的 pageId |
| `get-file` | `POST /api/rpc/command/get-file` | `id`, 可选 `page-id` | 获取完整文件数据，包含 `data.pages` 和 `data.pagesIndex` |
| `get-file-info` | `POST /api/rpc/command/get-file-info` | `id` | 轻量查询，仅返回 id |
| `get-file-summary` | `POST /api/rpc/command/get-file-summary` | `id` | 返回组件/变体/颜色/字体统计 |
| `rename-file` | `POST /api/rpc/command/rename-file` | `id`, `name` | 重命名文件 |
| `delete-file` | `POST /api/rpc/command/delete-file` | `id` | 删除文件，返回 204 |

### 团队

| API | 路径 | 参数 | 说明 |
|-----|------|------|------|
| `get-team-members` | `POST /api/rpc/command/get-team-members` | `team-id` | 返回团队成员列表，可用于校验团队一致性 |

### 分享

| API | 路径 | 参数 | 说明 |
|-----|------|------|------|
| `create-share-link` | `POST /api/rpc/command/create-share-link` | `file-id` | 创建文件分享链接 |

---

## 页面信息获取方法

由于 `list_pages` 等 API 不存在，按以下方式获取页面列表：

```javascript
// 1. 调用 get-file 获取文件数据
var fileData = callRpc("get-file", { id: fileId });

// 2. 从 data.pages 获取页面 ID 列表
var pageIds = fileData.data.pages;  // ["page-uuid-1", "page-uuid-2"]

// 3. 从 data.pagesIndex 获取页面详情
var pageInfo = fileData.data.pagesIndex[pageIds[0]];
// pageInfo.id   — 页面 ID
// pageInfo.name — 页面名称
```

---

## 页面管理方法

页面增删改 **不通过 REST API**，而是在 MCP Plugin 中使用 change 机制：

- **增加页面**: 发送 `add-page` change，指定 `name` 和 `id`
- **重命名页面**: 发送 `mod-page` change，指定 `id` 和 `name`
- **删除页面**: 发送 `del-page` change，指定 `id`

> 详细用法见 `mcp-generation-guide.md`。

---

## 认证

```
Authorization: Token <access-token>
```

Token 从 Penpot → Settings → Access Tokens 获取。

---

## 响应格式

默认 `Accept` 头决定返回值编码：

| Accept | 返回值 | 可读性 |
|--------|--------|--------|
| 不设置（默认） | `application/transit+json` | 不可读（如 `["^ ","~:email",...]`） |
| `application/json` | JSON | ✓ 可读 |

**始终设置 `Accept: application/json`。**
