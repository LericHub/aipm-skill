# Penpot API 与 MCP 连通性测试计划

## 背景

更新了 Penpot API 配置（`product_manager/references/penpot-api.yaml`）和 MCP 配置后，需要重新验证连通性，确保 product_manager 各节点能正常调用 Penpot 服务。

---

## 一、项目接口梳理

### 1.1 Penpot API 接口（HTTP REST）

配置位置：`product_manager/references/penpot-api.yaml`

当前配置参数：
- **BASE_URL**: `http://192.168.1.100:9001`
- **TEAM_ID**: `e6e20326-0802-8183-8007-f691bd15fa70`
- **PROJECT_ID**: `0075361e-ccf8-8015-8007-e050cd608729`
- **PAGE_ID**: `9df005f7-9c6f-80fa-8007-f695c370efe3`

| 接口 | 方法 | 路径 | 使用场景 |
|------|------|------|----------|
| get_profile | POST | /query/get-profile | 验证身份、获取团队信息 |
| create_file | POST | /command/create-file | drawUserStory 节点创建文件 |
| get_file | POST | /query/get-file | 获取文件信息 |
| list_files | POST | /query/list-files | 列出项目文件 |
| rename_file | POST | /command/rename-file | 重命名文件 |
| delete_file | POST | /command/delete-file | 删除文件 |
| add_page | POST | /command/add-page | genPrototype/detail 节点创建页面 |
| list_pages | POST | /query/list-pages | drawUserStory 节点获取页面列表 |
| rename_page | POST | /command/rename-page | drawUserStory 节点重命名默认页面 |
| delete_page | POST | /command/delete-page | 删除页面 |
| create_share_link | POST | /command/create-share-link | 创建分享链接 |

### 1.2 Penpot MCP 接口（本地/远程）

配置位置：各 skill 和 node 文档引用

| 工具 | 用途 | 使用场景 |
|------|------|----------|
| execute_code | 执行 Plugin API JS | 绘制图形、切换页面、清除内容 |
| high_level_overview | 获取页面结构摘要 | 绘制前检查当前状态 |
| penpot_api_info | 获取 API 文档 | 代码生成前参考 |
| export_shape | 导出形状为图片 | 验证绘制结果 |
| import_image | 导入图片 | 原型中使用图片资源 |

MCP 服务端点：`http://localhost:4401/mcp`（本地模式）

架构：
```
┌──────────────┐   MCP协议   ┌──────────────┐   WebSocket   ┌──────────────┐
│ AI Client    │ ─────────▶ │ MCP Server   │ ◀───────────▶ │ MCP Plugin   │
│ (本环境)      │            │ (本地/远程)   │               │ (Penpot内)   │
└──────────────┘            └──────────────┘               └──────────────┘
```

---

## 二、API 连通性测试用例

### 测试 1: 基础连通性 - get_profile
**目的**: 验证 Access Token 有效，服务可达
**请求**:
```bash
curl -X POST "http://192.168.1.100:9001/api/rpc/query/get-profile" \
  -H "Authorization: Token <PENPOT_ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{}'
```
**期望结果**:
- HTTP 200
- 返回用户 profile 信息（含 id, email, name 等）
- 确认返回的团队 ID 与配置的 TEAM_ID 一致

### 测试 2: 文件管理 - list_files
**目的**: 验证文件列表查询正常
**请求**:
```bash
curl -X POST "http://192.168.1.100:9001/api/rpc/query/list-files" \
  -H "Authorization: Token <PENPOT_ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"project-id": "0075361e-ccf8-8015-8007-e050cd608729"}'
```
**期望结果**:
- HTTP 200
- 返回文件列表（含 id, name 等字段）

### 测试 3: 文件管理 - get_file
**目的**: 验证获取指定文件信息正常
**请求**:
```bash
curl -X POST "http://192.168.1.100:9001/api/rpc/query/get-file" \
  -H "Authorization: Token <PENPOT_ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"id": "<existing-file-id>"}'
```
**期望结果**:
- HTTP 200
- 返回文件详细信息

### 测试 4: 页面管理 - list_pages
**目的**: 验证页面列表查询正常
**请求**:
```bash
curl -X POST "http://192.168.1.100:9001/api/rpc/query/list-pages" \
  -H "Authorization: Token <PENPOT_ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"file-id": "<existing-file-id>"}'
```
**期望结果**:
- HTTP 200
- 返回页面列表（含 id, name 字段）

### 测试 5: 页面管理 - add_page（写操作）
**目的**: 验证创建页面权限正常
**请求**:
```bash
curl -X POST "http://192.168.1.100:9001/api/rpc/command/add-page" \
  -H "Authorization: Token <PENPOT_ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"id": "<file-id>", "name": "test-page-connectivity"}'
```
**期望结果**:
- HTTP 200
- 返回新页面 ID
- **测试后清理**: 调用 delete_page 删除测试页面

### 测试 6: 错误处理验证
**目的**: 验证错误码返回格式正确
**请求**: 使用无效 Token 调用 get_profile
**期望结果**:
- HTTP 401 或特定错误响应
- 返回格式符合 error_codes 定义

---

## 三、MCP 连通性测试用例

### 测试 7: MCP Server 可达性
**目的**: 验证 MCP Server 服务正在运行
**方法**: 检查 `http://localhost:4401/mcp` 是否响应
**期望结果**:
- 服务可连接（HTTP 200 或 MCP 协议握手成功）
- 若连接失败，提示用户启动 MCP Plugin

### 测试 8: high_level_overview 工具
**目的**: 验证只读工具正常
**调用**:
```json
{
  "tool": "high_level_overview"
}
```
**期望结果**:
- 返回当前 Penpot 文件/页面的结构摘要
- 包含页面树、Board 列表等

### 测试 9: penpot_api_info 工具
**目的**: 验证 API 文档查询正常
**调用**:
```json
{
  "tool": "penpot_api_info",
  "arguments": {
    "type": "Penpot"
  }
}
```
**期望结果**:
- 返回 Penpot API 类型和方法文档

### 测试 10: execute_code 工具（读操作）
**目的**: 验证代码执行通道正常
**调用**:
```javascript
// 安全只读代码
return {
  currentPage: penpot.currentPage ? penpot.currentPage.name : null,
  selectionCount: penpot.selection ? penpot.selection.length : 0,
  rootChildrenCount: penpot.root ? penpot.root.children.length : 0
};
```
**期望结果**:
- 返回当前页面名称、选中元素数量、根节点子元素数量
- 无报错

### 测试 11: execute_code 工具（写操作 - 轻量）
**目的**: 验证写权限正常
**调用**:
```javascript
// 创建一个测试 Board，然后删除
var testBoard = penpot.createBoard();
testBoard.name = " connectivity-test-board";
testBoard.x = 0;
testBoard.y = 0;
testBoard.resize(100, 100);
testBoard.fills = [{ fillColor: "#E3F2FD", fillOpacity: 1 }];
penpot.currentPage.root.appendChild(testBoard);

return {
  created: true,
  boardId: testBoard.id,
  boardName: testBoard.name
};
```
**期望结果**:
- 成功创建 Board
- 返回 Board ID
- **测试后清理**: 执行删除代码

### 测试 12: export_shape 工具
**目的**: 验证导出功能正常
**调用**:
```json
{
  "tool": "export_shape",
  "arguments": {
    "shapeId": "selection",
    "format": "png"
  }
}
```
**期望结果**:
- 返回 PNG 图片数据（或文件路径）
- 图片可正常查看

---

## 四、测试执行顺序

```
Phase 1: API 基础连通性
  ├─ 测试1: get_profile（身份验证）
  ├─ 测试2: list_files（读权限）
  ├─ 测试3: get_file（文件读取）
  └─ 测试4: list_pages（页面读取）

Phase 2: API 写操作
  └─ 测试5: add_page → delete_page（写权限+清理）

Phase 3: API 错误处理
  └─ 测试6: 错误码验证

Phase 4: MCP 连通性（需要用户配置）
  ├─ 测试7: MCP Server 可达性
  ├─ 测试8: high_level_overview
  ├─ 测试9: penpot_api_info
  ├─ 测试10: execute_code（读）
  ├─ 测试11: execute_code（写+清理）
  └─ 测试12: export_shape
```

---

## 五、预期结果与问题处理

### API 测试预期
- 若测试 1 失败：检查 Token 是否过期、BASE_URL 是否正确、网络连通性
- 若测试 2-4 失败：检查 PROJECT_ID 是否正确、权限是否足够
- 若测试 5 失败：检查写权限、项目配额

### MCP 测试预期
- 若测试 7 失败：**需要用户先完成以下配置**：
  1. 在 Penpot 中打开 Plugins 面板（Ctrl+Alt+P / ⌘+Alt+P）
  2. 启动 MCP Plugin
  3. 确认 MCP Server 运行在 `http://localhost:4401/mcp`
  4. 或在 Penpot 账户设置中配置 Remote MCP（Integrations → MCP Server）

---

## 六、执行后输出

API 测试完成后，输出：
1. 各测试项通过/失败状态
2. 失败项的错误信息和诊断建议
3. 是否需要更新 `penpot-api.yaml` 配置

MCP 测试完成后，输出：
1. MCP Server 连接状态
2. 各工具调用结果
3. 是否需要用户调整配置
