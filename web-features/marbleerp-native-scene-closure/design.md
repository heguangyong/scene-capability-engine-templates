---
name: marbleerp-native-scene-closure
category: other
description: Template for Marbleerp Native Scene Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：MarbleERP 原生场景闭环（66-03）

## 设计重点

- 决策链从“提示文本”升级为“可追溯执行路径”
- 异常处置与成本影响并置展示
- 保持执行页的跨模块入口一致性

## 页面范围

- `MrpPlanningView`
- `ProductionRunDetailView`
- `ProcurementDetailView`
- `InventoryDetailView`
- `CostRecordDetailView`

## 关键设计

1. 决策输出卡增加来源、去向、触发条件。  
2. 异常场景增加影响范围（库存/成本）说明。  
3. 提供统一恢复动作（重排、回退、审批升级）入口。  

## 验证

- 页面交互回归（MRP -> 执行 -> 成本）
- `npx kse status --verbose`
- `npx kse doctor --docs`



## Ontology Model (Backfilled)

### Entities
- **MarbleerpNativeSceneClosureRecord**: Core domain record for Marbleerp Native Scene Closure scenarios.
- **MarbleerpNativeSceneClosureProcess**: Process context handling lifecycle transitions.
- **MarbleerpNativeSceneClosureAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **MarbleerpNativeSceneClosureRecord** 1:N **MarbleerpNativeSceneClosureProcess** (lifecycle orchestration).
- **MarbleerpNativeSceneClosureProcess** 1:N **MarbleerpNativeSceneClosureAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
