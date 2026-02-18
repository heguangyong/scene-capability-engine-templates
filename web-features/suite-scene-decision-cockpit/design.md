---
name: Suite Scene Decision Cockpit
category: web-features
description: Template for generating decision signals from scene execution data and presenting risk-driven actions in a cockpit UI.
difficulty: advanced
tags:
  - scene
  - decision
  - cockpit
  - risk
  - suite-hub
applicable_scenarios:
  - Building actionable decision signal cards for scene modules
  - Converting ER/BR/DL gaps into prioritized actions
  - Keeping decision insights synchronized with module switching
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：Suite 场景决策驾驶舱

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 基于当前模块的执行数据推导决策信号；  
2. 引入 `critical/warning/info` 信号语义；  
3. 将信号卡片嵌入执行钻取区域，保持信息连续。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可见决策驾驶舱并随模块切换

