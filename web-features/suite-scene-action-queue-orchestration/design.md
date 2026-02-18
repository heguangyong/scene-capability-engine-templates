---
name: Suite Scene Action Queue Orchestration
category: web-features
description: Template for mapping decision signals to actionable queues with owner/SLA assignment and checklist visibility.
difficulty: advanced
tags:
  - scene
  - action-queue
  - orchestration
  - sla
  - owner
applicable_scenarios:
  - Turning decision signals into execution queues
  - Assigning owners and SLAs by priority levels
  - Maintaining action-checklist visibility in suite hubs
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：Suite 场景行动队列编排

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 以 `sceneDecisionSignals` 为输入生成 `decisionActionQueue`；  
2. 按优先级映射 owner 与 SLA；  
3. 渲染行动队列卡片并展示 checklist。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可见行动队列并与模块切换联动

