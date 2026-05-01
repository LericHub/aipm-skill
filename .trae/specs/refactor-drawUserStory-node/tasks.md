# Tasks

- [x] Task 1: 创建 Penpot API 配置文件
  - [x] SubTask 1.1: 在 `product_manager/references/` 目录下创建 `penpot-api.md`
  - [x] SubTask 1.2: 记录文件管理 API 端点（create-file、get-file、list-files 等）
  - [x] SubTask 1.3: 记录页面管理 API 端点（add-page、list-pages、rename-page 等）
  - [x] SubTask 1.4: 记录每个端点的请求方法、URL、请求头、请求体参数、响应字段、错误码
  - [x] SubTask 1.5: 记录 API 认证方式（Access Token）

- [x] Task 2: 重构 drawUserStory 节点文件（05-drawUserStory.md）
  - [x] SubTask 2.1: 调整步骤顺序：4.1 创建文件 → 4.2 读取 PRD → 4.3 创建页面 → 4.4 绘制
  - [x] SubTask 2.2: 重写 4.1 创建/获取 Penpot 文件逻辑
    - [x] 调用真实 RPC API（POST /api/rpc/command/create-file）
    - [x] 成功：记录 fileId 和 projectUrl，更新 Memory.json
    - [x] 失败：提取 error message，展示错误，进入手动创建引导
  - [x] SubTask 2.3: 新增手动创建引导流程
    - [x] 发送 Penpot 地址和创建步骤
    - [x] 接收用户发送的文件 URL
    - [x] 解析 URL 中的 team-id 和 file-id
    - [x] 校验团队一致性
    - [x] 保存 fileId 和 projectUrl
  - [x] SubTask 2.4: 重写 4.2 读取 PRD 文档（原 4.1）
  - [x] SubTask 2.5: 重写 4.3 创建/获取 Penpot 页面（用户确认后）
  - [x] SubTask 2.6: 重写 4.4 绘制用户故事容器（修正编号）
  - [x] SubTask 2.7: 更新步骤 6 成功路径和失败路径的描述
  - [x] SubTask 2.8: 删除所有伪代码（penpotApi.createFile 等）
  - [x] SubTask 2.9: 确保步骤编号连续，无跳号无冗余

- [x] Task 3: 验证重构后的节点文件
  - [x] SubTask 3.1: 检查步骤编号是否连续（4.1 → 4.2 → 4.3 → 4.4 → 4.5）
  - [x] SubTask 3.2: 检查是否还有伪代码残留
  - [x] SubTask 3.3: 检查创建失败降级流程是否完整
  - [x] SubTask 3.4: 检查 Memory.json 更新逻辑是否正确
  - [x] SubTask 3.5: 检查与 _manage.md 和 SKILL.md 的引用一致性

# Task Dependencies
- Task 2 依赖 Task 1 完成（需要先定义 API 端点，才能在节点文件中引用）
- Task 3 依赖 Task 2 完成
