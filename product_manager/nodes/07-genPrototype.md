---
name: 07-genPrototype
description: 页面原型设计节点。读取PRD文档中的页面列表，在Penpot画布中绘制页面设计稿。
---

# 07 - GenPrototype (页面原型设计)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容

## 步骤2 职责
- 读取最新版本PRD文档
- 解析页面列表（从"页面详细设计"章节提取）
- 在Penpot中创建/获取原型设计页面
- 绘制页面设计稿
- 通知用户查看Penpot页面

---

## 步骤3 加载指引

### 前序节点产出物

从 Memory.json 获取：
- detail节点的file路径（PRD文档）
- penpot.fileId（Penpot文件ID）
- penpot.pages.prototype（原型页面ID，可能为空）

---

## 步骤4 执行逻辑

### 4.1 读取PRD文档

1. 调用 manage.read_memory() 获取项目信息
2. 读取当前版本的PRD文件：`./{项目名}_{datetime}/output/V{version}/PRD_{version}.md`
3. 从PRD文档的"页面详细设计"章节提取页面列表

### 4.2 创建/获取Penpot页面

1. 检查 Memory.json 中 `penpot.pages.prototype` 是否为空
2. 若为空：
   - 调用 `penpot.createPage()` 创建新页面
   - 页面命名：`页面原型 - V{version}`
   - 更新 Memory.json：`penpot.pages.prototype = 新页面ID`
3. 若已存在：
   - 调用 `penpot.openPage()` 切换到该页面
   - 清除页面上的旧内容（保留页面结构）

### 4.3 绘制页面设计稿

**容器结构：**
```
所有页面原型 (Board, flex column)
└── 页面: XXX (Board, flex column)
    ├── 页面标题 (Text)
    ├── 功能区域1 (Board)
    ├── 功能区域2 (Board)
    └── 操作按钮区 (Board, flex row)
```

**绘制规范：**

1. **父容器**（所有页面原型）：
   ```javascript
   var parentBoard = penpot.createBoard();
   parentBoard.name = "所有页面原型";
   parentBoard.resize(1200, 3000);
   parentBoard.fills = [{ fillColor: "#FAFAFA", fillOpacity: 1 }];
   var parentFlex = penpotUtils.addFlexLayout(parentBoard, "column");
   parentFlex.rowGap = 40;
   parentFlex.topPadding = 50;
   parentFlex.leftPadding = 50;
   parentFlex.rightPadding = 50;
   ```

2. **页面容器**：
   ```javascript
   var pageBoard = penpot.createBoard();
   pageBoard.name = "页面: " + pageName;
   pageBoard.resize(375, 812); // 手机页面尺寸
   pageBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
   pageBoard.borderRadius = 12;
   pageBoard.shadows = [{
     shadowColor: "#000000", shadowOpacity: 0.1,
     shadowOffsetX: 0, shadowOffsetY: 4, shadowBlur: 12
   }];
   var pageFlex = penpotUtils.addFlexLayout(pageBoard, "column");
   pageFlex.rowGap = 16;
   pageFlex.topPadding = 24;
   pageFlex.leftPadding = 24;
   pageFlex.rightPadding = 24;
   pageFlex.bottomPadding = 24;
   ```

3. **页面元素**：
   - 页面标题：大号文本（fontSize: 20）
   - 功能区域：带背景色的Board，包含功能描述
   - 操作按钮：带圆角的Rectangle或Text

### 4.4 绘制内容参考

根据PRD中每个页面的详细设计：
- 页面名称 → 页面标题
- 功能描述 → 功能区域内容
- 事件列表 → 操作按钮区
- 异常场景 → 标注在对应功能区域旁

---

## 步骤5 输出

### 5.1 保存到Memory.json

```json
{
  "penpot": {
    "pages": {
      "prototype": "page-uuid"
    }
  }
}
```

### 5.2 用户通知

在对话中发送：
```
页面原型设计稿已绘制完成！

🎨 Penpot页面：{penpot页面URL}

包含以下页面设计：
- 页面1: {名称}
- 页面2: {名称}
- ...

请打开Penpot页面查看并确认。确认后将继续进入genHTML节点生成HTML原型。
```

---

## 步骤6 完成后

1. 读取当前版本PRD文档
2. 解析页面列表
3. 创建/获取Penpot原型设计页面
4. 绘制页面设计稿
5. 验证无重叠
6. 更新Memory.json的penpot.pages.prototype
7. 展示产出摘要（页面列表+Penpot链接）
8. 等待用户确认
9. 确认后 → 调用 manage.update_node_status("genPrototype", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 genHTML 节点
