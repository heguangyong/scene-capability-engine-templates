---
name: wave-runner-automation
category: other
description: Template for Wave Runner Automation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：58-00 Wave Runner 自动化脚本

## 执行流程
1. 参数校验
2. 执行 sub wave（并行）
3. 判断退出码
4. 若成功，执行 master
5. 写入 summary JSON

## 日志策略
- sub log: `.kiro/config/wave-runner-sub-<timestamp>.log`
- main log: `.kiro/config/wave-runner-main-<timestamp>.log`
- summary: `.kiro/config/wave-runner-last.json`


## Ontology Model (Backfilled)

### Entities
- **WaveRunnerAutomationRecord**: Core domain record for Wave Runner Automation scenarios.
- **WaveRunnerAutomationProcess**: Process context handling lifecycle transitions.
- **WaveRunnerAutomationAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **WaveRunnerAutomationRecord** 1:N **WaveRunnerAutomationProcess** (lifecycle orchestration).
- **WaveRunnerAutomationProcess** 1:N **WaveRunnerAutomationAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
