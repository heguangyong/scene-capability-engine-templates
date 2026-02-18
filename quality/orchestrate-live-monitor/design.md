---
name: orchestrate-live-monitor
category: other
description: Template for Orchestrate Live Monitor
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：56-00 编排实时监控脚本

## 参数设计
- `-Workspace`：项目根目录，默认当前目录
- `-IntervalSec`：轮询间隔，默认 10 秒
- `-Once`：单次输出后退出

## 输出设计
- 统一时间戳前缀
- status 文件缺失时给出明确提示
- 进程计数独立展示，便于判断挂起/僵死


## Ontology Model (Backfilled)

### Entities
- **OrchestrateLiveMonitorRecord**: Core domain record for Orchestrate Live Monitor scenarios.
- **OrchestrateLiveMonitorProcess**: Process context handling lifecycle transitions.
- **OrchestrateLiveMonitorAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **OrchestrateLiveMonitorRecord** 1:N **OrchestrateLiveMonitorProcess** (lifecycle orchestration).
- **OrchestrateLiveMonitorProcess** 1:N **OrchestrateLiveMonitorAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
