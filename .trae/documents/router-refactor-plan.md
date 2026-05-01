# Router 重构计划：统一入口与知识卡片加载

## 背景

当前设计存在以下问题：
1. **Router 职责不完整**：README.md 中定义了 router 应做意图判断（新项目/继续/变更），但 01-router.md 中缺少这部分逻辑
2. **知识卡片加载重复**：10 个节点文件都包含「步骤1 知识卡片加载」，维护成本高
3. **入口逻辑分散**：「每次对话前检查 Memory.json」写在 router 里，但各业务节点又重复定义加载步骤

## 目标

1. **强化 Router 作为唯一入口**：每次对话必须先经过 router 进行意图判断和路由分发
2. **统一知识卡片加载**：由 router 统一加载知识卡片，业务节点不再重复定义
3. **消除重复代码**：删除各业务节点中的「步骤1 知识卡片加载」
4. **修复文件命名不一致**：clarify 节点输出文件改为 clarify.md（当前是 analysis.md）
5. **修复目录命名**：brainstorm 文件夹改为 analysis_process
6. **增强错误处理**：drawUserStory 节点 Penpot 操作失败时提示用户，不自动进入下一步
7. **统一页面编号规则**：修改为 `/{pageNameInEN}` 格式

## 当前状态分析

### 节点文件清单（10个）
| 文件 | 是否有「步骤1 知识卡片加载」 | 处理方案 |
|------|---------------------------|----------|
| 01-router.md | ✅ 有 | 强化为统一入口，增加意图判断逻辑 |
| _manage.md | ✅ 有（但侧重流程控制） | 保留 API 定义，删除加载步骤 |
| 02-brainstorm.md | ✅ 有 | 删除步骤1 |
| 03-clarify.md | ✅ 有 | 删除步骤1 |
| 04-analysis.md | ✅ 有 | 删除步骤1 |
| 05-drawUserStory.md | ✅ 有 | 删除步骤1，增加 Penpot 错误处理 |
| 06-detail.md | ✅ 有 | 删除步骤1 |
| 07-genPrototype.md | ✅ 有 | 删除步骤1 |
| 08-genHTML.md | ✅ 有 | 删除步骤1 |
| 09-change.md | ✅ 有 | 删除步骤1 |

### 用户确认机制检查结果

**所有业务节点输出后都会寻求用户确认** ✅

| 节点 | 确认位置 | 确认后动作 |
|------|----------|-----------|
| brainstorm | 步骤7 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| clarify | 步骤10 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| analysis | 步骤3 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| drawUserStory | 步骤8 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| detail | 步骤8 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| genPrototype | 步骤8 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| genHTML | 步骤10 等待用户确认 | update_node_status + transition_to_next_node + persist_memory |
| change | 步骤2 等待用户确认 | update_node_status + transition_to_first_affected_node + persist_memory |

**结论**：所有节点（router 除外）输出后都会等待用户确认，确认后调用 manage API 更新状态并流转到下一节点。机制一致，无需修改。

### 文件命名不一致问题

当前 clarify 节点的文件命名存在矛盾：

| 位置 | 当前命名 | 问题 |
|------|----------|------|
| 03-clarify.md 步骤4 | "输出到 clarify.md" | 说的是 clarify.md |
| 03-clarify.md 步骤9 | "文件名: analysis.md" | 实际写的是 analysis.md |
| 03-clarify.md 步骤10 | "写入 brainstorm/analysis.md" | 路径也是 analysis.md |
| _manage.md Memory.json | "brainstorm/analysis.md" | 示例中也是 analysis.md |
| 01-router.md 目录结构 | "brainstorm/analysis.md" | 目录结构中也是 analysis.md |

**需要统一改为 clarify.md**，同时文件夹 brainstorm 改为 analysis_process。

### 页面编号规则现状

当前页面编号存在多种格式：

| 位置 | 当前格式 | 示例 |
|------|----------|------|
| 06-detail.md 页面ID | PAGE-C-001 / PAGE-B-001 | 首页 = PAGE-C-001 |
| 06-detail.md 事件编号 | /home/feat-01 | 页面编号-feat-序号 |
| 06-detail.md 异常编号 | /home-feat-01-err01 | 事件编号-err序号 |

**需要统一为**：`/{pageNameInEN}` 格式，如 `/home`、`/productList`

### drawUserStory 节点 Penpot 错误处理现状

当前 [05-drawUserStory.md](file:///Users/bytealiez/Documents/Code/aipm-skill-CN/product_manager/nodes/05-drawUserStory.md) 的步骤6「完成后」：
1. 读取 PRD
2. 解析用户故事
3. 创建/获取 Penpot 页面
4. 绘制容器
5. 验证无重叠
6. 更新 Memory.json
7. 展示产出摘要
8. **等待用户确认**
9. 确认后 → 更新状态 → 流转到 detail

**问题**：步骤 3-6 的 Penpot 操作（创建页面、绘制容器）如果失败，没有错误处理逻辑，会直接跳到步骤 7 通知用户"已完成"，然后等待确认进入 detail。

### 知识卡片索引配置
- 文件：product_manager/references/knowledge_cards/index.md
- 内容：7张知识卡片的元数据、加载策略、各节点加载规则
- 当前「各节点加载规则」只定义了 BRAINSTORM/CLARIFY/ANALYSIS/DETAIL 四个节点

## 实施步骤

### 步骤1：重构 01-router.md
**操作**：重写 router 文件，使其成为每次对话的统一入口

**新增内容**：
1. **意图判断逻辑**：
   - 检查 Memory.json 是否存在
   - 存在 → 读取 current_node → 路由到对应节点
   - 不存在 → 新项目 → 进入 brainstorm
   - 检测用户输入关键词（"变更"/"改需求"等）→ 进入 change

2. **统一知识卡片加载**：
   - router 根据用户输入和 current_node 调用 load_relevant_knowledge_cards()
   - 将加载的知识卡片内容传递给目标节点

3. **路由分发表**：
   | 条件 | 目标节点 |
   |------|----------|
   | Memory.json 不存在 | brainstorm（新项目）|
   | current_node = brainstorm | brainstorm |
   | current_node = clarify | clarify |
   | current_node = analysis | analysis |
   | current_node = drawUserStory | drawUserStory |
   | current_node = detail | detail |
   | current_node = genPrototype | genPrototype |
   | current_node = genHTML | genHTML |
   | 用户说"变更"/"改需求" | change |

### 步骤2：删除各业务节点的「步骤1 知识卡片加载」
**操作**：从以下文件中删除重复的加载步骤：
- _manage.md
- 02-brainstorm.md
- 03-clarify.md
- 04-analysis.md
- 05-drawUserStory.md
- 06-detail.md
- 07-genPrototype.md
- 08-genHTML.md
- 09-change.md

**注意**：
- 06-detail.md 有额外逻辑「优先加载需求分析方法论」，需要评估是否保留或迁移到 index.md 的加载规则中
- 各节点步骤编号需要重新调整（步骤2→步骤1，步骤3→步骤2，以此类推）

### 步骤3：修复文件命名和目录命名
**操作**：

1. **clarify 输出文件改为 clarify.md**：
   - 修改 03-clarify.md：步骤4 "输出到clarify.md" 保持一致，步骤9/10 改为 clarify.md
   - 修改 _manage.md：Memory.json 示例中的文件路径改为 analysis_process/clarify.md
   - 修改 01-router.md：目录结构中的 analysis.md 改为 clarify.md
   - 修改 04-analysis.md：读取路径改为 analysis_process/clarify.md
   - 修改 02-brainstorm.md：目录结构说明中的 analysis.md 改为 clarify.md
   - 修改 product_manager/README.md：目录结构说明
   - 修改 product_manager/SKILL.md：目录结构说明

2. **brainstorm 文件夹改为 analysis_process**：
   - 修改 01-router.md：目录结构 brainstorm/ → analysis_process/
   - 修改 02-brainstorm.md：目录结构 brainstorm/ → analysis_process/
   - 修改 03-clarify.md：文件路径 brainstorm/ → analysis_process/
   - 修改 04-analysis.md：读取路径 brainstorm/ → analysis_process/
   - 修改 _manage.md：Memory.json 示例路径
   - 修改 product_manager/README.md：目录结构说明
   - 修改 product_manager/SKILL.md：目录结构说明

### 步骤4：修改页面编号规则为 /{pageNameInEN}
**操作**：

1. **修改 04-analysis.md**：
   - 步骤2 职责中「页面编号生成规则」改为：`/{pageNameInEN}`
   - 页面列表中的页面ID示例改为：`/home`、`/productList`、`/productDetail`

2. **修改 06-detail.md**：
   - 页面ID 列表示例：`PAGE-C-001` → `/home`
   - 事件编号规范：`页面编号-feat-序号` → `/{pageNameInEN}/feat-{序号}`
   - 事件列表示例：`/home/feat-01`、`/home/feat-02`
   - 异常编号规范：`事件编号-err序号` → `/{pageNameInEN}/feat-{序号}/err-{序号}`
   - 异常列表示例：`/home/feat-01/err-01`

3. **修改 product_manager/README.md**：
   - 事件编号说明：`页面编号-feat-序号` → `/{pageNameInEN}/feat-{序号}`

4. **修改 product_manager/nodes/README.md**：
   - 如有页面编号相关说明，同步更新

### 步骤5：增强 drawUserStory 节点 Penpot 错误处理
**操作**：修改 05-drawUserStory.md

**当前问题**：
- 步骤4.2 创建/获取 Penpot 页面：如果 `penpot.createPage()` 或 `penpot.openPage()` 失败，没有处理
- 步骤4.3 绘制容器：如果绘制失败，没有处理
- 步骤6 完成后：无论 Penpot 操作是否成功，都通知用户"已完成"

**修改内容**：
1. **在步骤4.2 增加错误检查**：
   ```
   4.2 创建/获取 Penpot 页面
   1. 检查 Memory.json 中 penpot.fileId 是否为空
      - 若为空：提示用户在 Penpot 中创建新文件...
   2. 检查 penpot.pages.userStory 是否为空
   3. 若为空：
      - 调用 penpot.createPage() 创建新页面
      - ⚠️ 如果创建失败：
        - 记录错误原因
        - 向用户展示错误信息："Penpot 页面创建失败：{错误原因}"
        - 停止执行，不进入下一步
        - 等待用户处理后再重试
   ```

2. **在步骤4.3 增加错误检查**：
   ```
   4.3 绘制用户故事容器
   - 调用 penpot API 绘制容器结构
   - ⚠️ 如果绘制失败：
     - 记录错误原因
     - 向用户展示错误信息："Penpot 绘制失败：{错误原因}"
     - 停止执行，不进入下一步
     - 等待用户处理后再重试
   ```

3. **修改步骤6 完成后**：
   ```
   6. 更新 Memory.json 的 penpot.pages.userStory（仅在成功时）
   7. 展示产出摘要
      - 如果 Penpot 操作成功：展示用户故事列表 + Penpot 链接
      - 如果 Penpot 操作失败：展示错误信息 + 重试指引
   8. 等待用户确认
      - 成功时：确认后进入 detail
      - 失败时：用户处理完问题后重试
   ```

### 步骤6：更新知识卡片索引的「各节点加载规则」
**操作**：在 index.md 中补充缺失节点的加载规则

**当前缺失**：
- drawUserStory
- genPrototype
- genHTML
- change

**需要决定**：这些节点是否需要加载知识卡片？加载哪些？

### 步骤7：验证一致性
**操作**：
1. 检查 router 中的加载逻辑与 index.md 的「加载策略」是否一致
2. 检查各节点删除步骤1后，剩余步骤是否连贯
3. 检查 manage.md 的 API 定义是否仍然有效
4. 检查所有文件中的路径引用是否统一为 analysis_process/clarify.md
5. 检查页面编号规则是否统一为 `/{pageNameInEN}`
6. 检查 drawUserStory 的错误处理逻辑是否完整

## 风险与注意事项

1. **drawUserStory/genPrototype/genHTML/change 节点原本有加载步骤**：需要确认这些节点是否真的不需要知识卡片，还是在 router 中统一处理
2. **06-detail.md 的特殊逻辑**：「优先加载需求分析方法论」是 detail 特有的，需要在 router 中保留或通过加载规则配置
3. **_manage.md 的双重角色**：既是工具库（API 定义），又被当作节点文件（有步骤1），需要理清边界
4. **文件路径变更影响**：brainstorm → analysis_process 和 analysis.md → clarify.md 的变更需要确保所有引用文件同步更新
5. **页面编号规则变更影响**：所有引用页面编号的地方需要同步更新，包括示例数据、模板、规范说明
6. **Penpot 错误处理**：需要确保错误信息清晰，用户知道如何重试

## 预期结果

1. 01-router.md 成为完整的意图判断和路由分发中心
2. 所有业务节点不再包含「步骤1 知识卡片加载」
3. 知识卡片加载逻辑统一收敛到 router 和 index.md
4. clarify 节点输出文件统一为 analysis_process/clarify.md
5. brainstorm 目录统一为 analysis_process
6. 页面编号规则统一为 `/{pageNameInEN}`
7. drawUserStory 节点 Penpot 操作失败时正确提示用户，不自动进入下一步
8. 减少重复代码，提高可维护性
