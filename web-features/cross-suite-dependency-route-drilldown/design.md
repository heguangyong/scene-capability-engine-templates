---
name: Cross-Suite Dependency Route Drilldown
category: web-features
description: Template for exposing cross-suite dependency paths and enabling route-level drilldown in scene execution panels.
difficulty: advanced
tags:
  - scene
  - dependency
  - drilldown
  - suite-hub
  - routing
applicable_scenarios:
  - Showing dependency routes per execution step
  - Navigating between related modules across suites
  - Reducing ambiguity in cross-suite execution chains
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：跨套件依赖路径钻取

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 在执行矩阵每行追加“依赖跳转”列；  
2. 在执行面板底部汇总“依赖路径”标签；  
3. 依赖项附带 route 所属 suite 信息，形成跨域认知。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 依赖路径可见且可跳转

