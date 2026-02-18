---
name: workflow-approval-engine
category: other
description: Template for Workflow Approval Engine
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：工作流与审批引擎（60-10）

## 设计要点

- 提供独立工作流服务与统一 API
- 业务模块通过流程绑定表接入审批
- 前端提供流程设计、待办和审批中心

## 验收

- 流程可配置并可发布执行
- 多业务模块可复用同一引擎
- 审批日志可检索可追溯

