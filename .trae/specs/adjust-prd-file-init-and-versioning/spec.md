# PRD文件初始化及版本管理调整 Spec

## Why
1. 现有逻辑中各节点创建独立目录和文件，文档分散难以统一管理，版本管理混乱
2. 需求变更场景下无法有效追溯历史版本和精准定位变更点
3. 变更流程缺少结构化的影响分析和版本同步机制
4. 需支持"文本内容作为LLM记忆留存，画布作为可视化展示"的未来规划

## What Changes
- **目录结构调整**：项目根目录下包含brainstorm/和output/两个主目录
  - `brainstorm/`：包含brainstorm.md、clarify.md，不纳入版本管理
  - `output/{Version}/`：包含PRD_{Version}.md和prototype/目录，纳入版本管理
- **PRD模板结构化**：在analysis节点创建PRD文档框架，通过大标题区分节点负责人
- **变更影响分析**：在change节点增加结构化的影响分析和回退策略判断
- **节点精简**：删除writing节点，prototyping节点仅生成静态HTML文件
- **版本管理范围**：brainstorm和clarify不纳入版本管理，analysis~prototyping纳入版本管理

## Impact
- Affected specs：目录结构规范、节点内容写入逻辑、版本管理规范、变更处理流程
- Affected code：nodes/_manage.md、nodes/01-router.md、nodes/02-brainstorm.md、nodes/03-clarify.md、nodes/04-analysis.md、nodes/05-detail.md、nodes/06-prototyping.md、nodes/08-change.md

## ADDED Requirements

### Requirement: 目录结构初始化
系统 SHALL 在brainstorm节点完成时创建项目根目录结构：
1. 创建 `/项目名称_{datetime}/brainstorm/` 目录
2. 创建 `/项目名称_{datetime}/output/` 目录（初始为空）
3. 在brainstorm/目录生成brainstorm.md文件

#### Scenario: 目录结构初始化成功
- **WHEN** 用户完成brainstorm节点内容提交
- **THEN** 系统自动创建项目根目录结构，包含brainstorm/和output/两个主目录，brainstorm.md文件写入brainstorm/目录

### Requirement: brainstorm内容整合写入
系统 SHALL 在clarify节点完成时将brainstorm和clarify内容整合写入brainstorm/analysis.md文件。

#### Scenario: clarify节点内容整合成功
- **WHEN** 用户完成clarify节点内容提交
- **THEN** 系统在brainstorm/目录生成analysis.md文件，包含brainstorm产出内容和clarify产出内容，追加写入

### Requirement: PRD文档框架创建
系统 SHALL 在analysis节点完成时创建PRD文档框架和版本目录。

#### Scenario: PRD文档框架创建成功
- **WHEN** 用户完成analysis节点内容提交
- **THEN** 系统在output/V1/目录生成PRD_V1.md文件，框架包含：
```
# PRD文档 - V1.0

## 1. 项目概述
### 1.1 背景与目标 [brainstorm/clarify节点产出]
### 1.2 用户角色

## 2. 需求分析
### 2.1 业务流程图 [analysis节点产出]
### 2.2 页面列表
### 2.3 功能列表

## 3. 详细设计
### 3.1 页面流程图 [detail节点产出]
### 3.2 事件列表
### 3.3 异常场景

## 4. 页面详细设计
### 4.1 {页面名称1}
#### 4.1.1 页面说明
#### 4.1.2 功能描述
#### 4.1.3 业务流程图
#### 4.1.4 事件列表
#### 4.1.5 异常场景
### 4.2 {页面名称2}
...

## 5. 原型与交互
### 5.1 原型索引 [prototyping节点产出]
### 5.2 页面原型引用

## 6. 变更记录
### 6.1 变更历史 [change节点维护]
```

**说明**：
- "页面详细设计"章节按页面名称组织，支持精确定位变更点
- 每个页面下包含页面说明、功能描述、业务流程图、事件列表、异常场景
- 变更时可按页面名称快速定位需要更新的内容

### Requirement: PRD内容追加写入
系统 SHALL 在detail和prototyping节点完成时，将内容追加到当前版本的PRD文档对应章节。

#### Scenario: detail节点内容追加成功
- **WHEN** 用户完成detail节点内容提交
- **THEN** 系统查找output/V{Version}/PRD_{Version}.md文件，在"详细设计"章节追加detail节点产出内容

#### Scenario: prototyping节点内容追加成功
- **WHEN** 用户完成prototyping节点内容提交
- **THEN** 系统在PRD文档"原型与交互"章节更新原型引用路径，在output/V{Version}/prototype/目录生成HTML文件

### Requirement: 变更影响分析
系统 SHALL 在change节点处理需求变更时，先进行结构化的影响分析和回退策略判断。

#### Scenario: 变更影响分析流程
- **WHEN** 用户在change节点提交需求变更
- **THEN** 系统执行以下步骤：
  1. 分析变更内容涉及的功能模块
  2. 根据变更类型判断需要重新分析的节点
  3. 按大标题定位PRD文档中需要更新的章节
  4. 按页面名称定位需要更新的具体页面
  5. 复制当前PRD文档为新版本文件
  6. 复制当前prototype目录为新版本目录
  7. 在新版本文件中更新受影响章节
  8. 更新Memory.json的版本号和文件路径

#### Scenario: 变更类型判断逻辑
| 变更类型 | 判断依据 | 回退节点 |
|---------|---------|---------|
| 想法/新方向 | 用户提交的是一个想法 | brainstorm |
| 功能（涉及业务流程调整或需提交文档） | 用户提交的是功能，涉业务流程调整 | clarify |
| 功能（涉及新页面和业务） | 用户提交功能，涉新页面和流程调整 | analysis |
| 文案/布局调整 | 仅涉及文案调整或页面布局 | detail |

#### Scenario: PRD板块定位规则
- **按大标题定位节点产出**：根据变更涉及的节点，在对应大标题下更新产出内容
- **按页面名称定位具体内容**：在节点产出内按页面名称查找需要更新的页面和事件

#### Scenario: 变更时版本号管理
- **WHEN** 变更涉及前序内容修改
- **THEN** 系统自动递增major版本号（V1.x → V2.0）
- **WHEN** 变更仅为新增功能
- **THEN** 系统自动递增minor版本号（V1.0 → V1.1）

### Requirement: PRD模板章节归属
系统 SHALL 通过PRD文档的大标题（一级标题）区分不同节点的负责内容和产出。

#### Scenario: PRD章节与节点映射
| PRD大标题 | 负责节点 | 说明 |
|-----------|---------|------|
| 项目概述 | brainstorm/clarify | 需求背景和目标 |
| 需求分析 | analysis | 业务流程和页面列表 |
| 详细设计 | detail | 事件列表和异常场景 |
| 页面详细设计 | detail | 按页面维度组织详细内容，支持按页面名称定位 |
| 原型与交互 | prototyping | 原型引用和交互说明 |
| 变更记录 | change | 增量变更历史 |

## MODIFIED Requirements

### Requirement: 目录初始化时机调整
**原逻辑**：各节点分别创建自己的目录
**新逻辑**：仅在brainstorm节点创建项目根目录，后续节点在版本目录下操作

### Requirement: 节点文件路径调整
**原逻辑**：各节点在output/V{Version}/{node_name}/目录下生成文件
**新逻辑**：
- brainstorm/clarify内容：写入brainstorm/analysis.md
- analysis产出：创建PRD框架
- detail产出：追加到PRD文档对应章节
- prototyping产出：生成prototype/目录下的HTML文件

### Requirement: 变更回退策略调整
**原逻辑**：根据受影响节点列表回退到第一个受影响节点重新执行
**新逻辑**：分析变更内容，精准定位PRD文档中需要更新的章节，判断需要回退的节点

## REMOVED Requirements

### Requirement: writing节点删除
**原因**：删除writing节点，prototyping节点不生成PRD查看器
**替代方案**：用户可通过直接查看PRD文档或原型HTML文件了解需求详情
**Migration**：将writing节点的功能（生成PRD查看器）改为prototyping节点直接生成静态HTML原型