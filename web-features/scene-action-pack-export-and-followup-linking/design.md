---
name: Scene Action Pack Export and Follow-up Linking
category: web-features
description: Template for exporting action packs from action queues and linking follow-up workflows for agents and operators.
difficulty: advanced
tags:
  - scene
  - action-pack
  - export
  - follow-up
  - orchestration
applicable_scenarios:
  - Producing copy-ready action packs for distributed execution
  - Linking action queue outputs to follow-up workflows
  - Preserving runbook and traceability features while exporting action packs
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：场景行动包导出与跟进联动

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 基于 `decisionActionQueue` 组装行动包 Markdown；  
2. 使用统一剪贴板写入函数输出导出反馈；  
3. 保持导出逻辑与运行中的模块上下文一致。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可复制行动包并显示通知

