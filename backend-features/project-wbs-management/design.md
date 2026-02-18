---
name: project-wbs-management
category: other
description: Template for Project Wbs Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：项目与 WBS 管理（60-06）

## 设计要点

- 新增 Project/WBS 服务与 REST 接口
- 在订单、采购、生产、库存实体增加项目维度引用
- 前端新增项目总览与 WBS 树

## 验收

- 项目与 WBS 可完整 CRUD
- 单据可关联项目并汇总成本
- 项目看板可展示进度与成本



## Ontology Model (Backfilled)

### Entities
- **ProjectWbsManagementRecord**: Core domain record for Project Wbs Management scenarios.
- **ProjectWbsManagementProcess**: Process context handling lifecycle transitions.
- **ProjectWbsManagementAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **ProjectWbsManagementRecord** 1:N **ProjectWbsManagementProcess** (lifecycle orchestration).
- **ProjectWbsManagementProcess** 1:N **ProjectWbsManagementAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
