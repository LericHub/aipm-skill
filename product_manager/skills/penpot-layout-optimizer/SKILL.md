---
name: "penpot-layout-optimizer"
description: "Optimizes Penpot container dimensions and layout to eliminate excessive whitespace while maintaining visual cleanliness. Invoke when Penpot pages look too empty or containers have too much unused space."
---

# Penpot Layout Optimizer

Optimizes Penpot container dimensions and spacing to eliminate excessive whitespace while keeping the layout clean and organized.

## When to Use

- Penpot pages look "too empty" or sparse
- Containers have large unused areas
- Child elements are floating in oversized parent boards
- Need to make layouts more compact without losing readability
- After programmatic generation, containers need dimension tuning

## Core Principles

1. **Content-Driven Sizing**: Container dimensions should be driven by actual content, not arbitrary fixed values
2. **Consistent Spacing**: Use uniform padding and gap values (rhythm: 8/12/16/24)
3. **Visual Hierarchy**: Maintain clear separation between sections while avoiding excess
4. **Auto-Sizing First**: Prefer `auto` sizing over fixed dimensions when possible

## Optimization Strategies

### Strategy 1: Parent Container Auto-Sizing

Make parent containers adapt to their children:

```javascript
// Set vertical sizing to auto so parent grows with content
if (parentBoard.flex) {
  parentBoard.flex.verticalSizing = "auto";
  parentBoard.flex.topPadding = 30;
  parentBoard.flex.bottomPadding = 30;
  parentBoard.flex.leftPadding = 40;
  parentBoard.flex.rightPadding = 40;
}
```

### Strategy 2: Calculate Minimum Required Height

Calculate height based on actual children:

```javascript
function calculateOptimalHeight(board, gapSize) {
  var children = board.children;
  var totalHeight = 0;
  var visibleCount = 0;
  
  children.forEach(function(child) {
    if (!child.hidden) {
      totalHeight += child.height;
      visibleCount++;
    }
  });
  
  // Add gaps between children
  var gaps = visibleCount > 1 ? (visibleCount - 1) * gapSize : 0;
  
  // Add padding (top + bottom)
  var padding = board.flex ? 
    (board.flex.topPadding + board.flex.bottomPadding) : 40;
  
  return totalHeight + gaps + padding;
}
```

### Strategy 3: Section Container Tuning

For nested section containers (like "业务流程图", "页面地图"):

```javascript
// Adjust section containers to fit their content
sections.forEach(function(section) {
  if (section.flex) {
    section.flex.verticalSizing = "auto";
    section.flex.topPadding = 12;
    section.flex.bottomPadding = 12;
    section.flex.leftPadding = 16;
    section.flex.rightPadding = 16;
  }
  
  // Set minimum height based on content type
  var minHeight = section.name.indexOf("流程") >= 0 ? 140 : 90;
  section.resize(section.width, Math.max(minHeight, calculateOptimalHeight(section, 6)));
});
```

### Strategy 4: Flow Area Optimization

For horizontal flow areas:

```javascript
flowAreas.forEach(function(area) {
  // Ensure enough height for content
  area.resize(area.width, 80);
  
  if (area.flex) {
    area.flex.alignItems = "center";
    area.flex.verticalPadding = 10;
  }
});
```

## Standard Spacing Values

| Element Type | Top/Bottom Padding | Left/Right Padding | Internal Gap |
|-------------|-------------------|-------------------|--------------|
| Root Container | 30px | 40px | 30px |
| Story Card | 16px | 20px | 12px |
| Section Board | 12px | 16px | 6px |
| Flow Area | 10px | 10px | 8px (row) |

## Complete Optimization Workflow

```javascript
function optimizeLayout(rootBoard) {
  // Step 1: Optimize all story cards
  var storyCards = penpotUtils.findShapes(
    function(s) { return s.name.indexOf("用户故事:") === 0; },
    rootBoard
  );
  
  storyCards.forEach(function(card) {
    if (card.flex) {
      card.flex.verticalSizing = "auto";
    }
    // Compact height: content + gaps + padding
    card.resize(card.width, 320);
  });
  
  // Step 2: Optimize section containers
  var sections = penpotUtils.findShapes(
    function(s) { 
      return s.name === "业务流程图" || s.name === "页面地图"; 
    },
    rootBoard
  );
  
  sections.forEach(function(section) {
    if (section.flex) {
      section.flex.verticalSizing = "auto";
      section.flex.topPadding = 12;
      section.flex.bottomPadding = 12;
    }
    var isFlow = section.name.indexOf("流程") >= 0;
    section.resize(section.width, isFlow ? 160 : 100);
  });
  
  // Step 3: Optimize flow areas
  var flowAreas = penpotUtils.findShapes(
    function(s) { 
      return s.name === "流程绘制区" || s.name === "页面流"; 
    },
    rootBoard
  );
  
  flowAreas.forEach(function(area) {
    var isPageFlow = area.name.indexOf("页面") >= 0;
    area.resize(area.width, isPageFlow ? 60 : 100);
    if (area.flex) {
      area.flex.alignItems = "center";
    }
  });
  
  // Step 4: Optimize root container
  if (rootBoard.flex) {
    rootBoard.flex.verticalSizing = "auto";
  }
  
  // Calculate optimal height
  var totalContentHeight = storyCards.reduce(function(sum, card) {
    return sum + card.height;
  }, 0);
  var gaps = (storyCards.length - 1) * 30;
  var padding = 60;
  var newHeight = totalContentHeight + gaps + padding;
  
  // Enforce minimum height
  if (newHeight < 600) newHeight = 600;
  
  rootBoard.resize(rootBoard.width, newHeight);
  
  return {
    storyCardsOptimized: storyCards.length,
    sectionsOptimized: sections.length,
    flowAreasOptimized: flowAreas.length,
    finalHeight: newHeight
  };
}
```

## Before/After Comparison

| Metric | Before | After |
|--------|--------|-------|
| Story Card Height | 400px (fixed) | 320px (content-driven) |
| Business Flow Height | 120px | 160px (optimized) |
| Page Map Height | 70px | 100px (optimized) |
| Root Container | 2400px (fixed) | ~1940px (auto-calculated) |
| Whitespace Ratio | ~40% | ~15% |

## Validation Checklist

After optimization, verify:
- [ ] All text elements remain fully visible
- [ ] No child elements overflow their containers
- [ ] Spacing between elements is consistent
- [ ] Visual hierarchy is preserved
- [ ] No overlapping elements
- [ ] Page remains scrollable if content exceeds viewport
