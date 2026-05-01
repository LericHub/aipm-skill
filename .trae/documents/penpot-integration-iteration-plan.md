# PenPot 集成迭代实施计划

## 目标
将 AIPM Skill 与 PenPot 深度集成，实现：
1. 用户故事可视化（DrawUserStory 节点）
2. 页面原型设计（genPrototype 节点）
3. 设计稿转 HTML（genHtml 节点增强）

## 实施步骤

### 第一阶段：基础设施与配置

#### 1.1 PenPot API Token 配置
- 在 `.trae/config.json` 或环境变量中配置 PenPot API Token
- 在 `_manage.md` 中增加 PenPot 连接配置说明
- 验证 Token 可用性（测试创建/读取项目）

#### 1.2 Memory.json 扩展
- 新增 `penpot` 字段结构：
  ```json
  {
    "penpot": {
      "projectUrl": "https://design.penpot.app/#/workspace?...",
      "fileId": "uuid",
      "pages": {
        "userStory": "page-uuid",
        "prototype": "page-uuid",
        "prd": "page-uuid"
      }
    }
  }
  ```
- 在 `_manage.md` 中更新 Memory.json 结构定义

#### 1.3 目录结构确认
- 确认 `penpot-helper-skill/` 已包含：
  - `references/user-story-layout-guide.md`（已创建）
  - `references/mcp-generation-guide.md`（已有）
  - 其他必要参考文档

---

### 第二阶段：新增 DrawUserStory 节点（05-drawUserStory.md）

#### 2.1 节点定位
- 插入在 **analysis** 之后、**detail** 之前
- 文件命名：`05-drawUserStory.md`
- 后续节点编号顺延：detail → 06、genPrototype → 07、genHtml → 08、change → 09

#### 2.2 节点逻辑
1. **读取 analysis 产出**
   - 从 Memory.json 获取 PRD 文件路径
   - 解析用户故事列表（从 PRD 的"需求分析"章节提取）

2. **创建/获取 PenPot 页面**
   - 检查 Memory.json 中是否已有 `userStory` page ID
   - 无则调用 `penpot.createPage()` 创建新页面
   - 页面命名：`用户故事 - V{version}`

3. **绘制用户故事容器**
   - 创建父容器 Board（所有用户故事）
   - 为每个用户故事创建子容器 Board
   - 使用 Flex 布局（column）确保无重叠
   - 容器结构：
     ```
     所有用户故事 (flex column)
     └── 用户故事: XXX (flex column)
         ├── 标题文本
         ├── 描述文本
         └── 流程容器 (flex row)
             ├── 业务流程 (flex column)
             │   ├── 标题
             │   └── 内容
             └── 页面流程 (flex column)
                 ├── 标题
                 └── 内容
     ```

4. **保存状态**
   - 更新 Memory.json 的 `penpot.pages.userStory`
   - 记录页面 URL 供用户访问

5. **用户通知**
   - 在对话中发送 PenPot 页面链接
   - 引导用户查看并确认

#### 2.3 与现有节点衔接
- 完成后进入 **detail** 节点
- detail 节点可读取 PenPot 上的用户故事进行细化

---

### 第三阶段：新增 genPrototype 节点（07-genPrototype.md）

#### 3.1 节点定位
- 插入在 **detail** 之后、**genHtml** 之前
- 文件命名：`07-genPrototype.md`

#### 3.2 节点逻辑
1. **读取最新 PRD**
   - 从 Memory.json 获取当前版本 PRD 路径
   - 解析页面列表（从"页面详细设计"章节提取）

2. **创建/获取 PenPot 页面**
   - 检查 Memory.json 中是否已有 `prototype` page ID
   - 无则调用 `penpot.createPage()` 创建新页面
   - 页面命名：`页面原型 - V{version}`

3. **绘制页面设计稿**
   - 为每个页面创建 Board 容器
   - 使用 Flex 布局组织页面元素
   - 参考 PRD 中的页面描述绘制：
     - 页面标题
     - 主要功能区域
     - 按钮和交互元素占位
   - 容器结构：
     ```
     所有页面原型 (flex column)
     └── 页面: XXX (flex column)
         ├── 页面标题
         ├── 功能区域1
         ├── 功能区域2
         └── 操作按钮区
     ```

4. **保存状态**
   - 更新 Memory.json 的 `penpot.pages.prototype`

5. **用户通知**
   - 发送 PenPot 页面链接
   - 引导用户查看设计稿

---

### 第四阶段：增强 genHtml 节点（08-genHtml.md）

#### 4.1 现有逻辑保留
- 读取 detail 节点产出
- 使用 TDesign 组件库生成 HTML

#### 4.2 新增设计稿读取逻辑
1. **连接 PenPot**
   - 从 Memory.json 获取 `prototype` page ID
   - 切换到原型设计页面

2. **读取设计稿内容**
   - 遍历页面列表
   - 使用 `penpot.generateMarkup()` 生成 HTML 结构
   - 使用 `penpot.generateStyle()` 生成 CSS 样式

3. **生成 index 页面**
   - 创建入口 HTML 文件
   - 平铺展示所有生成的页面链接
   - 结构：
     ```html
     <div class="prototype-index">
       <h1>项目原型 - V{version}</h1>
       <div class="page-list">
         <a href="page_首页.html">首页</a>
         <a href="page_登录.html">登录</a>
         ...
       </div>
     </div>
     ```

4. **打包输出**
   - 将所有 HTML 文件打包为 zip
   - 输出路径：`output/V{version}/prototype.zip`
   - 告知用户文件地址

---

### 第五阶段：变更处理逻辑

#### 5.1 需求变更场景
- **用户故事变更**：
  - 按名称定位 PenPot 上的用户故事容器
  - 更新内容（保留容器结构，替换文本）
  - 版本号递增规则：minor+1

- **页面设计变更**：
  - 按名称定位 PenPot 上的页面容器
  - 更新设计稿内容
  - 同步更新 HTML 原型

#### 5.2 避免重叠策略
- 使用 Flex 布局自动排列
- 更新时先清除旧内容再重新绘制
- 保持容器结构不变，只替换内部元素

---

### 第六阶段：文档更新

#### 6.1 SKILL.md 更新
- 更新工作流图（新增 DrawUserStory、genPrototype）
- 更新节点映射表
- 更新输出文件结构说明

#### 6.2 README.md 更新
- 新增节点说明
- 更新目录结构
- 更新工作流图

#### 6.3 _manage.md 更新
- 更新 Memory.json 结构
- 更新节点流转顺序
- 更新版本管理规则

---

### 第七阶段：测试验证

#### 7.1 功能测试
- [ ] DrawUserStory 节点创建页面成功
- [ ] 用户故事容器无重叠
- [ ] genPrototype 节点创建设计稿成功
- [ ] genHtml 生成 HTML 成功
- [ ] index 页面正确展示所有入口

#### 7.2 变更测试
- [ ] 用户故事变更后 PenPot 内容更新
- [ ] 页面设计变更后 HTML 同步更新
- [ ] 版本号正确递增

#### 7.3 边界测试
- [ ] 无 PenPot 连接时的降级处理
- [ ] 大量用户故事时的性能
- [ ] 复杂页面设计稿的生成

---

## 文件变更清单

| 文件 | 操作 | 说明 |
|------|------|------|
| `nodes/05-drawUserStory.md` | 新增 | 用户故事绘制节点 |
| `nodes/06-detail.md` | 修改 | 编号调整（原05） |
| `nodes/07-genPrototype.md` | 新增 | 页面原型设计节点 |
| `nodes/08-genHtml.md` | 修改 | 增强设计稿读取（原07） |
| `nodes/09-change.md` | 修改 | 编号调整（原08） |
| `nodes/_manage.md` | 修改 | 更新Memory.json结构 |
| `nodes/01-router.md` | 修改 | 更新节点流转顺序 |
| `SKILL.md` | 修改 | 更新工作流和节点说明 |
| `README.md` | 修改 | 更新节点说明和目录结构 |
| `penpot-helper-skill/references/user-story-layout-guide.md` | 已有 | 无需修改 |

---

## 风险与应对

| 风险 | 影响 | 应对措施 |
|------|------|---------|
| PenPot API 超时 | 节点执行失败 | 增加重试机制，简化绘制逻辑 |
| 设计稿转 HTML 质量差 | 原型不可用 | 保留 TDesign 生成作为备选 |
| 大量元素性能问题 | 页面卡顿 | 分页绘制，限制单次元素数量 |
| 用户未连接 PenPot | 功能不可用 | 提供降级方案（仅文本输出） |

---

## 实施顺序

1. **第一周**：第一阶段（基础设施）+ 第二阶段（DrawUserStory）
2. **第二周**：第三阶段（genPrototype）+ 第四阶段（genHtml 增强）
3. **第三周**：第五阶段（变更处理）+ 第六阶段（文档更新）
4. **第四周**：第七阶段（测试验证）+ 修复优化
