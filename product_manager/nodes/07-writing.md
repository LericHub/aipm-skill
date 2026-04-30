---
name: 07-writing
description: PRD整合与渲染技能。整合前序节点产出物，生成完整的PRD文档和可交互的HTML需求展示系统。
---

# 07 - Writing (PRD撰写)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容，作为本节点后续输出的参考基础

## 步骤2 职责边界

本节点负责将前序节点的产出整合为可交付的 PRD 文档和交互式 HTML 系统：

- 整合 DETAIL 和 PROTOTYPING 两个前序节点的最终版本文件
- 生成需求概述页面 (overview.html)
- 将 Markdown 页面文档转换为 HTML
- 生成交互式 PRD 查看器 (index.html)
- 完成后进入下一节点

**不负责**：原型生成（已在 node6 完成）、迭代修改（外部流程）、打包压缩（下游行为）

---

## 步骤3 Step 1: 上下文读取与初始化

### 1.1 读取前序节点产出

每次从文件读取 Memory.json（不依赖上下文缓存），从 node_list 获取前序节点的 file 路径：

| 节点 | file 路径 | 内容 |
|------|-----------|------|
| node5 (detail) | 从 node_list 中 name="detail" 的 file 字段获取 | 需求详细设计产出物 |
| node6 (prototyping) | 从 node_list 中 name="prototyping" 的 file 字段获取 | 交互原型文件目录 |

### 1.2 读取实际文件

根据获取的 file 路径，读取实际文件内容：
- detail 文件：提取项目背景、目标、页面列表、业务流程
- prototyping 目录：扫描 app/ 和 web/ 子目录获取原型文件列表

### 1.3 确定版本信息

从 Memory.json → project 读取版本号，格式：`V{version}`（如 V1.0）

---

## 步骤4 Step 2: 初始化输出目录

### 2.1 目录定位

输出目录为 `writing` 节点专属目录：`./output/V{version}/writing/`
如果目录不存在，则创建它。

### 2.2 确保目录结构

```
./output/V{version}/writing/
├── doc/              # 页面需求文档
│   ├── app/
│   └── web/
├── html/             # 概述页面
└── assets/           # 静态资源（Logo等）
```

### 2.3 复制 Logo 资源

初始化时复制 logo.png 到 assets 目录：
```bash
mkdir -p ./output/V{version}/writing/assets/
cp /Users/bytealiez/Documents/Code/aipm-skill/assets/logo.png ./output/V{version}/writing/assets/logo.png
```

注意：原型文件仍保留在 `./output/V{version}/prototyping/` 目录中，本节点仅引用它们，不复制。

---

## 步骤5 Step 3: 原型文件与端信息映射

### 3.1 读取原型文件与端信息

- 获取 `prototyping` 节点产出目录（如 `./output/V{version}/prototyping/`），扫描 app/ 和 web/ 子目录获取原型文件列表。
- 根据 `detail` 节点提取的 C端/B端页面列表获取页面的端信息（平台归属）。

### 3.2 页面路径映射

为每个页面建立映射关系：
- `prototypePath`: 根据平台归属使用对应的子目录路径
  - C端页面: `../prototyping/app/page_{name}.html`
  - B端页面: `../prototyping/web/page_{name}.html`
- 如果找不到对应原型，不要丢弃该页面，仍保留它（设置 prototypePath 为空或展示占位），保证文档能够被查阅。

---

## 步骤6 Step 4: 生成需求概述页面 (overview.html)

### 4.1 内容来源

从 node5 (detail) 的 file 路径读取文件内容，提取：
- 项目背景、目标、边界
- 用户角色
- C端页面列表 + B端页面列表
- 业务流程图（从 DetailDesign 的 FullPicture 部分提取）

### 4.2 生成规则

- 直接将 Markdown 内容转换为 HTML
- 保持 Markdown 的原始结构和层级
- 支持 Mermaid 图表渲染
- 添加亮色主题样式
- **不使用模板**，按转换规则生成

### 4.3 保存路径

`./output/V{version}/writing/html/overview.html`

---

## 步骤7 Step 5: 生成页面需求文档 (Markdown → HTML)

### 5.1 内容来源

**从 node5 (detail) 的 file 路径读取文件，提取第 4 部分**：

```
## 4. 以页面的维度，输出细分事件列表和细化需求
   ### 4.1 首页
   ### 4.2 商品列表
   ...
```

### 5.2 生成规则

- 每个页面独立生成一个 HTML 文件
- **直接转换**，不做内容修改
- 支持 Mermaid 图表
- 保持 Markdown 原始层级

### 5.3 保存路径

| 端 | 路径 |
|---|------|
| C端 | `./output/V{version}/writing/doc/app/{page}.html` |
| B端 | `./output/V{version}/writing/doc/web/{page}.html` |

### 5.4 生成后立即保存

**生成一个保存一个，不要等全部完成再保存**

---

## 步骤8 Step 6: 生成交互式 PRD 查看器 (index.html)

### 6.1 使用模板

使用 `../references/prd_viewer_template.html` 作为模板

### 6.2 替换占位符

| 占位符 | 替换为 |
|--------|--------|
| `{{PROJECT_NAME}}` | 项目名称 |
| `{{VERSION}}` | 版本号 |
| `{{DATE}}` | 日期 |
| `{{LOGO_PATH}}` | `assets/image.png` |
| `{{C_END_MENU_ITEMS}}` | C端菜单项 HTML |
| `{{B_END_MENU_ITEMS}}` | B端菜单项 HTML |
| `let currentPageData = {...}` | 完整的页面数据对象 |

### 6.3 页面数据格式

结合 Step 3 的映射结果生成：
```javascript
{
  "overview": {"title": "需求概述"},
  "home": {
    "title": "首页",
    "platform": "app", // 或 "web"
    "prototypePath": "../prototyping/page_home.html",
    "docPath": "doc/app/home.html"
  }
}
```

### 6.4 查看器交互

- 点击"需求概述"：工作区显示单视图 `html/overview.html`（隐藏双栏）
- 点击页面按钮：工作区显示双视图，左侧 iframe 加载 `prototypePath`，右侧 iframe 加载 `docPath`。

### 6.5 保存路径

`./output/V{version}/writing/index.html`

---

## 步骤9 Step 7: 验证与完成

### 7.1 验证清单

- [ ] overview.html 已生成
- [ ] 所有页面文档已生成（doc/app/*.html, doc/web/*.html）
- [ ] index.html 已生成
- [ ] 所有原型文件可访问

### 7.2 更新 Memory

每次从文件读取 Memory.json，更新当前节点状态：

```markdown
[current_node_status]: # (DRAFT)
[updated_at]: # ({datetime})
[node_list]: # (
  ...更新 writing 节点的 file 字段为 ./output/V{version}/writing/
)
```

写入当前节点产出物路径到 node_list 的对应节点。

---

## 步骤10 完成后动作

1. 展示产出摘要
2. 等待用户确认
3. 确认后 → 打包输出目录：`zip -r output/{project_name}_V{version}_output_{YYYYMMDD_HHmm}.zip output/V{version}/`
4. 确认后 → `manage.update_node_status("writing", "CONFIRM")`
5. 确认后 → `manage.transition_to_next_node()`
6. 确认后 → `manage.persist_memory()`
7. 进入下一节点

---

## 步骤11 附录：Markdown 转 HTML 规则

| 元素 | 转换规则 |
|------|----------|
| 标题 | `#` → `<h1>`, `##` → `<h2>` |
| 列表 | `-` → `<ul><li>`, `1.` → `<ol><li>` |
| 代码块 | ``` → `<pre><code>`，Mermaid 块保留原样 |
| 链接 | `[text](url)` → `<a>` |
| 图片 | `![alt](url)` → `<img>` |
| 表格 | Markdown 表格 → HTML `<table>` |
| 粗体/斜体 | `**` → `<strong>`, `*` → `<em>` |

**Mermaid 图表**：保留 ` ```mermaid ` 代码块，引入 mermaid.js 自动渲染

---

## 步骤12 输出目录结构

```
./output/V{version}/writing/
├── index.html              # 交互式 PRD 查看器
├── assets/
│   └── image.png           # Logo
├── html/
│   └── overview.html       # 需求概述（单视图）
└── doc/
    ├── app/
    │   └── *.html          # C端页面需求文档
    └── web/
        └── *.html          # B端页面需求文档
```
注意：原型不在此目录下，而是引用 `../prototyping/` 中的平铺文件。