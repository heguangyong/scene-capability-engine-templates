---
name: Suite Scene Execution Playbook
category: web-features
description: Template for building a scene execution playbook panel with phase/action drilldown and ER/BR/DL mapping.
difficulty: advanced
tags:
  - scene
  - execution
  - playbook
  - suite-hub
  - drilldown
applicable_scenarios:
  - Creating execution step matrices from scene blueprints
  - Providing module-level scene drilldown for operators
  - Keeping execution views aligned with scene canvas data
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：Suite 场景执行手册

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`
- `frontend/src/renderer/config/scene-blueprints.js`（仅读取）

## 关键设计

1. 复用 `sceneCanvasCards` 作为执行钻取导航数据源；  
2. 根据模块蓝图推导执行步骤矩阵（阶段/动作/ER/BR/DL）；  
3. 支持“进入模块”快捷入口，保持路由一致性。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可见执行钻取与步骤矩阵

