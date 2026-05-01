---
name: 05-drawUserStory
description: 用户故事可视化节点。读取analysis产出的PRD文档，在Penpot画布中绘制用户故事的业务流程和页面流程。
---

# 05 - DrawUserStory (用户故事绘制)

## 步骤1 知识卡片加载
1. 识别当前用户输入内容中的核心话题/关键词
2. 读取知识卡片索引文件：product_manager/references/knowledge_cards/index.md
3. 根据识别出的话题匹配index中对应激活场景的知识卡片
4. 读取所有匹配成功的知识卡片完整内容

## 步骤2 职责
- 读取analysis节点产出的PRD文档
- 解析用户故事列表
- 在Penpot中创建/获取用户故事页面
- 绘制用户故事容器结构
- 通知用户查看Penpot页面

---

## 步骤3 加载指引

### 前序节点产出物

从 Memory.json 获取：
- analysis节点的file路径（PRD文档）
- penpot.fileId（Penpot文件ID）
- penpot.pages.userStory（用户故事页面ID，可能为空）

---

## 步骤4 执行逻辑

### 4.1 读取PRD文档

1. 调用 manage.read_memory() 获取项目信息
2. 读取analysis节点的PRD文件：`./{项目名}_{datetime}/output/V{version}/PRD_{version}.md`
3. 从PRD文档的"需求分析"章节提取用户故事列表

### 4.2 创建/获取Penpot页面

1. 检查 Memory.json 中 `penpot.fileId` 是否为空
   - 若为空：提示用户在Penpot中创建新文件，提供文件URL，保存 `penpot.fileId`
2. 检查 Memory.json 中 `penpot.pages.userStory` 是否为空
3. 若为空：
   - 调用 `penpot.createPage()` 创建新页面
   - 页面命名：`用户故事 - V{version}`
   - 更新 Memory.json：`penpot.pages.userStory = 新页面ID`
4. 若已存在：
   - 调用 `penpot.openPage()` 切换到该页面
   - 清除页面上的旧内容（保留页面结构）

### 4.3 绘制用户故事容器

**容器结构：**
```
所有用户故事 (Board, flex column)
└── 用户故事: XXX (Board, flex column)
    ├── 标题文本 (Text)
    ├── 描述文本 (Text)
    └── 流程容器 (Board, flex row)
        ├── 业务流程 (Board, flex column)
        │   ├── 标题 (Text)
        │   └── 内容 (Text)
        └── 页面流程 (Board, flex column)
            ├── 标题 (Text)
            └── 内容 (Text)
```

**绘制规范（参考 penpot-helper-skill）：**

1. **父容器**（所有用户故事）：
   ```javascript
   var parentBoard = penpot.createBoard();
   parentBoard.name = "所有用户故事";
   parentBoard.resize(1200, 2000);
   parentBoard.fills = [{ fillColor: "#FAFAFA", fillOpacity: 1 }];
   var parentFlex = penpotUtils.addFlexLayout(parentBoard, "column");
   parentFlex.rowGap = 30;
   parentFlex.topPadding = 50;
   parentFlex.leftPadding = 50;
   parentFlex.rightPadding = 50;
   ```

2. **用户故事容器**：
   ```javascript
   var storyBoard = penpot.createBoard();
   storyBoard.name = "用户故事: " + storyName;
   storyBoard.resize(1100, 500);
   storyBoard.fills = [{ fillColor: "#FFFFFF", fillOpacity: 1 }];
   storyBoard.borderRadius = 8;
   storyBoard.shadows = [{
     shadowColor: "#000000", shadowOpacity: 0.1,
     shadowOffsetX: 0, shadowOffsetY: 2, shadowBlur: 8
   }];
   var storyFlex = penpotUtils.addFlexLayout(storyBoard, "column");
   storyFlex.rowGap = 20;
   storyFlex.topPadding = 20;
   storyFlex.leftPadding = 20;
   storyFlex.rightPadding = 20;
   storyFlex.bottomPadding = 20;
   ```

3. **流程容器**：
   ```javascript
   var flowContainer = penpot.createBoard();
   flowContainer.name = "流程容器";
   flowContainer.resize(1040, 400);
   flowContainer.fills = [];
   var flowFlex = penpotUtils.addFlexLayout(flowContainer, "row");
   flowFlex.columnGap = 20;
   ```

4. **业务流程/页面流程容器**：
   ```javascript
   var bizFlow = penpot.createBoard();
   bizFlow.name = "业务流程";
   bizFlow.resize(500, 380);
   bizFlow.fills = [{ fillColor: "#E3F2FD", fillOpacity: 1 }];
   bizFlow.borderRadius = 4;
   var bizFlex = penpotUtils.addFlexLayout(bizFlow, "column");
   bizFlex.rowGap = 10;
   bizFlex.topPadding = 15;
   bizFlex.leftPadding = 15;
   ```

### 4.4 防止重叠检查

绘制完成后，验证：
- [ ] 所有文本元素都是某个Board的子元素
- [ ] 所有包含文本的Board都有Flex布局
- [ ] 文本之间无重叠（间距 > 0）

---

## 步骤5 输出

### 5.1 保存到Memory.json

```json
{
  "penpot": {
    "pages": {
      "userStory": "page-uuid"
    }
  }
}
```

### 5.2 用户通知

在对话中发送：
```
用户故事已绘制完成！

📋 Penpot页面：{penpot页面URL}

包含以下用户故事：
- 用户故事1: {名称}
- 用户故事2: {名称}
- ...

请打开Penpot页面查看并确认。确认后将继续进入Detail节点。
```

---

## 步骤6 完成后

1. 读取analysis节点的PRD文档
2. 解析用户故事列表
3. 创建/获取Penpot用户故事页面
4. 绘制用户故事容器结构
5. 验证无重叠
6. 更新Memory.json的penpot.pages.userStory
7. 展示产出摘要（用户故事列表+Penpot链接）
8. 等待用户确认
9. 确认后 → 调用 manage.update_node_status("drawUserStory", "CONFIRM")
10. 确认后 → 调用 manage.transition_to_next_node()
11. 确认后 → 调用 manage.persist_memory()
12. 进入 Detail 节点
