---
name: kiro-state-transition-diagnosis
category: other
description: Template for Kiro State Transition Diagnosis
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 状态迁移诊断 — 设计文档

## 目标

定位并修复状态迁移链路中的不一致、遗漏与回退失败问题，确保状态机闭环可追踪。

## 诊断模型

1. **状态定义层**：状态枚举、迁移规则、禁行边。
2. **执行层**：服务调用、事务提交、错误回滚。
3. **可观测层**：日志、审计事件、失败重放证据。

## 关键机制

- 统一状态迁移入口，禁止旁路写状态。
- 对每条迁移边增加前置条件与后置断言。
- 失败时记录迁移上下文并触发可重复回放。

## 验证要点

1. 合法迁移全部通过，不合法迁移全部拦截。
2. 迁移失败时状态不应部分写入。
3. 审计链路可完整追溯“触发-执行-结果”。


## Ontology Model (Backfilled)

### Entities
- **KiroStateTransitionDiagnosisRecord**: Core domain record for Kiro State Transition Diagnosis scenarios.
- **KiroStateTransitionDiagnosisProcess**: Process context handling lifecycle transitions.
- **KiroStateTransitionDiagnosisAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **KiroStateTransitionDiagnosisRecord** 1:N **KiroStateTransitionDiagnosisProcess** (lifecycle orchestration).
- **KiroStateTransitionDiagnosisProcess** 1:N **KiroStateTransitionDiagnosisAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
