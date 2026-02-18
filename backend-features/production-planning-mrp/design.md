---
name: production-planning-mrp
category: other
description: Template for Production Planning Mrp
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：生产计划与 MRP 增强（60-03）

## 设计要点

- 新增计划服务层：需求、MRP、计划订单、异常消息
- 对接 `BomService/RoutingService/InventoryService/ProcurementService`
- 前端增加计划看板与异常处理视图

## 验收

- 支持一键 MRP 计算并输出计划订单
- 可从计划订单转生产/采购单
- 产能负载基础指标可查询



## Ontology Model (Backfilled)

### Entities
- **ProductionPlanningMrpRecord**: Core domain record for Production Planning Mrp scenarios.
- **ProductionPlanningMrpProcess**: Process context handling lifecycle transitions.
- **ProductionPlanningMrpAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **ProductionPlanningMrpRecord** 1:N **ProductionPlanningMrpProcess** (lifecycle orchestration).
- **ProductionPlanningMrpProcess** 1:N **ProductionPlanningMrpAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
