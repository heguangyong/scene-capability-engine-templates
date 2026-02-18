---
name: Suite Scene Canvas Visualization
category: web-features
description: Template for visualizing suite scene composition cards, ER/BR/DL summaries, and key flow chains in SuiteHub.
difficulty: intermediate
tags:
  - scene
  - suite-hub
  - visualization
  - moqui
  - er-br-dl
applicable_scenarios:
  - Building a scene canvas view across multiple suites
  - Making ER/BR/DL and key flows visible in one hub page
  - Improving module routing from scene cards
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：Suite 场景画布可视化

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`
- `frontend/src/renderer/config/scene-blueprints.js`（仅读取）

## 关键设计

1. 从 `suites.modules.route` 反查 `scene blueprint`；  
2. 渲染“场景画布卡片”：场景摘要、ER/BR/DL、keyFlows；  
3. 卡片点击进入对应模块。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可见画布区且交互正常


