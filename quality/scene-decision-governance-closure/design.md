---
name: scene-decision-governance-closure
category: other
description: Template for Scene Decision Governance Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：场景决策治理收口（70-03）

## 范围

- 70 系列 spec 文档与 scene/package 资产
- `docs/handoffs/evidence/*` 收口证据
- `.kiro/steering/CURRENT_CONTEXT.md` 与 `docs/handoffs/template-export-list.md`

## 关键设计

1. 固化 `build + status + doctor + final-sync` 验证链；  
2. 对 `70-00~70-03` 执行 package/ontology/template 导出；  
3. 同步治理证据和上下文索引，保持下一轮可无缝衔接。  

## 验证

- `npx kse status --verbose`
- `npx kse doctor --docs`
- `powershell -File scripts/kse-final-sync.ps1 -Round r70-scene-decision-cockpit-20260218 -OntologyMinAverageScore 70 -OntologyMinValidRate 100`


## Ontology Model (Backfilled)

### Entities
- **SceneDecisionGovernanceClosureRecord**: Core domain record for Scene Decision Governance Closure scenarios.
- **SceneDecisionGovernanceClosureProcess**: Process context handling lifecycle transitions.
- **SceneDecisionGovernanceClosureAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **SceneDecisionGovernanceClosureRecord** 1:N **SceneDecisionGovernanceClosureProcess** (lifecycle orchestration).
- **SceneDecisionGovernanceClosureProcess** 1:N **SceneDecisionGovernanceClosureAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
