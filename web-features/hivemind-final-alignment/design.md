---
name: hivemind-final-alignment
category: other
description: Template for Hivemind Final Alignment
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：HiveMind 最终对齐（65-01）

## 设计重点

- 以项目协同场景为中心，而非单页功能堆叠。
- 将审批规则从“后端存在”提升为“前端可见可理解”。
- 保留现有路由结构，优先补齐页面内语义与表单深度。

## 关键设计

1. 在项目/任务详情页补充场景摘要区（实体关系 + 规则 + 决策）。  
2. 将审批状态与任务状态联动提示前置到主操作区。  
3. 为关键表单补充字段规则说明与失败恢复提示。

## 验证

- 页面交互回归（项目 -> 任务 -> 审批 -> 回退）
- `npx kse status --verbose`
- `npx kse doctor --docs`



## Ontology Model (Backfilled)

### Entities
- **HivemindFinalAlignmentRecord**: Core domain record for Hivemind Final Alignment scenarios.
- **HivemindFinalAlignmentProcess**: Process context handling lifecycle transitions.
- **HivemindFinalAlignmentAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **HivemindFinalAlignmentRecord** 1:N **HivemindFinalAlignmentProcess** (lifecycle orchestration).
- **HivemindFinalAlignmentProcess** 1:N **HivemindFinalAlignmentAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
