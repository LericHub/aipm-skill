---
name: 08-genHTML
description: HTML原型生成技能。基于Penpot设计稿或TDesign组件生成高保真HTML原型、index入口页面并打包。
---

# 08 - GenHTML (HTML原型生成)

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

### Penpot设计稿读取

从 Memory.json 获取：
- penpot.pages.prototype（原型设计页面ID）
- 若存在，切换到该页面读取设计稿内容

---

## 步骤5 HTML生成逻辑

### 5.1 生成方式选择

根据Penpot设计稿可用性选择生成方式：

**方式A：基于Penpot设计稿生成（优先）**
- 条件：Memory.json中 `penpot.pages.prototype` 存在
- 步骤：
  1. 切换到Penpot原型页面
  2. 遍历所有页面Board
  3. 对每个页面调用 `penpot.generateMarkup()` 生成HTML
  4. 调用 `penpot.generateStyle()` 生成CSS
  5. 合并为完整HTML文件

**方式B：基于TDesign组件生成（备选）**
- 条件：Penpot设计稿不可用
- 步骤：
  1. 读取detail节点的PRD内容
  2. 使用TDesign MCP Skill生成组件
  3. 组装为HTML文件

### 5.2 目录结构

```
./{项目名称}_{datetime}/output/V{Version}/prototype/
├── index.html              # 原型索引页面（平铺所有页面入口）
├── app/
│   ├── page_{page_name1}.html
│   └── page_{page_name2}.html
└── web/
    ├── page_{page_name1}.html
    └── page_{page_name2}.html
```

### 5.3 index页面生成

生成入口HTML文件，平铺展示所有页面：

```html
<!DOCTYPE html>
<html>
<head>
  <title>项目原型 - V{version}</title>
  <style>
    body { font-family: -apple-system, sans-serif; padding: 40px; background: #f5f5f5; }
    .container { max-width: 1200px; margin: 0 auto; }
    h1 { text-align: center; margin-bottom: 40px; }
    .page-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 24px; }
    .page-card { background: white; border-radius: 12px; padding: 24px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
    .page-card h3 { margin-top: 0; }
    .page-card a { display: inline-block; padding: 12px 24px; background: #0066FF; color: white; text-decoration: none; border-radius: 6px; }
  </style>
</head>
<body>
  <div class="container">
    <h1>项目原型 - V{version}</h1>
    <div class="page-grid">
      <div class="page-card">
        <h3>首页</h3>
        <p>主要功能：商品浏览、搜索、个人中心</p>
        <a href="app/page_首页.html">查看页面</a>
      </div>
      <!-- 更多页面卡片... -->
    </div>
  </div>
</body>
</html>
```

### 5.4 输出路径

- 文件名: `page_{page_name}.html`
- 路径: `./{项目名称}_{datetime}/output/V{Version}/prototype/{platform}/`
  - 平台子目录: `app/` (C端页面) / `web/` (B端页面)
  - `page_{page_name}.html`: 各页面原型文件
  - `index.html`: 原型索引页面
- 生成规则: 见 [01-router.md](01-router.md) 中的文件生成规则
- **注意**: 必须按平台归属分别存放到 app/ 或 web/ 子目录

### 5.5 PRD文档更新

genHTML节点完成后，需要更新PRD文档"原型与交互"章节（## 5）的原型引用路径。

---

## 步骤6 打包输出

### 6.1 打包HTML文件

将所有生成的HTML文件打包为zip：
- 源目录：`./{项目名称}_{datetime}/output/V{Version}/prototype/`
- 输出文件：`./{项目名称}_{datetime}/output/V{Version}/prototype.zip`

### 6.2 用户通知

在对话中发送：
```
HTML原型已生成完成！

📦 文件地址：{项目路径}/output/V{version}/prototype.zip

包含：
- index.html（入口页面）
- app/ 目录（C端页面）
- web/ 目录（B端页面）

解压后打开 index.html 即可查看所有页面原型。
```

---

## 步骤7 完成后

1. 创建prototype目录：`./{项目名称}_{datetime}/output/V{Version}/prototype/`
2. 检查Penpot设计稿可用性
3. 若可用：基于Penpot设计稿生成HTML（方式A）
4. 若不可用：基于TDesign组件生成HTML（方式B）
5. 生成index入口页面
6. 更新PRD文档"原型与交互"章节（## 5）的原型引用路径
7. 打包HTML文件为zip
8. 调用 manage.update_node_output("genHTML", "{项目名称}_{datetime}/output/V{Version}/prototype/")
9. 展示产出摘要（文件地址+页面列表）
10. 等待用户确认
11. 确认后 → 调用 manage.update_node_status("genHTML", "CONFIRM")
12. 确认后 → 调用 manage.transition_to_next_node()
13. 确认后 → 调用 manage.persist_memory()
14. 进入 Change 节点（如无变更则项目完成）
