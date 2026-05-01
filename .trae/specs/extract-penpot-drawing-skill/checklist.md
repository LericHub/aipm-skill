# Checklist

## 目录结构
- [x] `product_manager/skills/penpot-drawing-skill/` 目录已创建
- [x] `product_manager/skills/penpot-drawing-skill/references/` 目录已创建

## SKILL.md 主文件
- [x] 文件头部包含正确的 name 和 description
- [x] 触发条件包含 drawUserStory 和 genPrototype 场景
- [x] 前置条件说明必须先加载 penpot-helper-skill
- [x] 指引索引正确指向 user-story-drawing-guide.md 和 prototype-drawing-guide.md

## user-story-drawing-guide.md
- [x] 包含容器结构说明（Board树）
- [x] 包含业务流程图绘制代码（4.4.3 节内容）
- [x] 包含页面地图绘制代码（4.4.4 节内容）
- [x] 包含防止重叠检查清单（4.5 节内容）
- [x] 引用路径正确（指向 penpot-helper-skill 中的通用规则）

## prototype-drawing-guide.md
- [x] 包含容器结构说明
- [x] 包含页面设计稿绘制代码
- [x] 包含页面元素绘制规范

## 05-drawUserStory.md 修改
- [x] 步骤3 加载指引已更新为引用 penpot-drawing-skill
- [x] 步骤4 执行逻辑保留核心流程，引用 guide 中的绘制代码
- [x] 移除了内嵌的绘制代码（约500行）
- [x] 引用路径与实际文件位置一致

## 07-genPrototype.md 修改
- [x] 步骤3 加载指引已更新为引用 penpot-drawing-skill
- [x] 步骤4 执行逻辑保留核心流程，引用 guide 中的绘制代码
- [x] 移除了内嵌的绘制代码（约100行）
- [x] 引用路径与实际文件位置一致

## 代码完整性验证
- [x] 抽取后 guide 中的代码与原节点文件中的代码一致
- [x] 引用关系正确，无死链接
- [x] 原节点文件删减后逻辑仍然完整
