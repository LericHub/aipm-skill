---
name: 07-genHTML
description: HTML原型生成技能。基于详细设计生成 ASCII 线框图（自动展示），并使用 tdesign-component-helper Skill（如果已安装）或直接基于 TDesign 组件库生成高保真 HTML 原型。
---

# 07 - GenHTML (HTML原型生成)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容，作为本节点后续输出的参考基础

## 步骤2 职责
- 生成 ASCII 线框图和 HTML 原型
- Mock 数据生成（欧莱雅兰蔻数据）
- ASCII 线框图生成
- HTML 原型生成（tdesign-component-helper）
- 使用 Design Reference Skill 和 TDesign MCP Skill 确定页面风格和组件
- 实时打印要求
- **禁止后台任务规范**：本节点必须在前台运行，逐个页面生成以便实时展示和用户确认
- 逐个页面生成规范

---

## 步骤3 Skill 集成

### Design Reference Skill (prototyping-design-reference)
- 位置: `.opencode/skills/prototyping-design-reference/`
- 功能: 提供设计系统指南（颜色、字体、组件样式）
- 引用文件: `uber_design.md`

### TDesign MCP Skill (tdesign-mcp-skill)
- 位置: `.opencode/skills/tdesign-mcp-skill/`
- 功能: 基于 mock 数据结构推荐 TDesign 组件
- MCP 配置: `mcp.json` 中的 tdesign-mcp-server

### 工作流程
1. 生成 Mock 数据后
2. 调用 Design Reference Skill 获取设计指南
3. 调用 TDesign MCP Skill 获取组件推荐
4. 合并两个 Skill 的结果，确定最终页面风格和组件

---

## 步骤4 加载指引

### 前序节点产出物

读取 detail 节点的产出物：
- 从 Memory.json 获取 detail 的 file 路径
- 调用 manage.read_memory() 获取 node_list
- 读取该文件内容

---

## 步骤5 输出文件

### 5.1 目录结构

```
./{项目名称}_{datetime}/output/V{Version}/prototype/
├── index.html              # 原型索引页面
├── app/
│   ├── page_{page_name1}.html
│   └── page_{page_name2}.html
└── web/
    ├── page_{page_name1}.html
    └── page_{page_name2}.html
```

### 5.2 输出路径

- 文件名: `page_{page_name}.html`
- 路径: `./{项目名称}_{datetime}/output/V{Version}/prototype/{platform}/`
  - 平台子目录: `app/` (C端页面) / `web/` (B端页面)
  - `page_{page_name}.html`: 各页面原型文件
  - `index.html`: 原型索引页面
- 生成规则: 见 [01-router.md](01-router.md) 中的文件生成规则
- **注意**: 必须按平台归属分别存放到 app/ 或 web/ 子目录

### 5.3 PRD文档更新

genHTML节点完成后，需要更新PRD文档"原型与交互"章节（## 5）的原型引用路径。

---

## 步骤6 完成后

1. 创建prototype目录：`./{项目名称}_{datetime}/output/V{Version}/prototype/`
2. 按平台生成HTML原型文件：`prototype/app/` 和 `prototype/web/`
3. 生成原型索引页面：`prototype/index.html`
4. 更新PRD文档"原型与交互"章节（## 5）的原型引用路径
5. 调用 manage.update_node_output("genHTML", "{项目名称}_{datetime}/output/V{Version}/prototype/")
6. 展示产出摘要
7. 等待用户确认
8. 确认后 → 调用 manage.update_node_status("genHTML", "CONFIRM")
9. 确认后 → 调用 manage.transition_to_next_node()
10. 确认后 → 调用 manage.persist_memory()
11. 进入 Change 节点（如无变更则项目完成）
