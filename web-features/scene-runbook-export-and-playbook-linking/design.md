---
name: Scene Runbook Export and Playbook Linking
category: web-features
description: Template for exporting module-scoped runbook markdown and linking execution playbooks to multi-agent handoff workflows.
difficulty: advanced
tags:
  - scene
  - runbook
  - playbook
  - export
  - handoff
applicable_scenarios:
  - Exporting scene execution context for human or agent handoff
  - Generating copy-ready runbooks from active module data
  - Connecting playbook drilldown with governance evidence
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 设计文档：场景 Runbook 导出与联动

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 为当前 active module 生成 Runbook Markdown；  
2. 复用剪贴板与通知逻辑输出结果；  
3. 确保 Runbook 内容与执行矩阵、依赖、决策驾驶舱同源。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可复制 Runbook，通知语义正确

