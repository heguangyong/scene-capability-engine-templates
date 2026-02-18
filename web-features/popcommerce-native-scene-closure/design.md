---
name: popcommerce-native-scene-closure
category: other
description: Template for Popcommerce Native Scene Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：PopCommerce 原生场景闭环（66-02）

## 设计重点

- 异常处理语义统一，减少跨页面认知切换
- 链路卡从“导航信息”提升为“决策与恢复信息”
- 保持订单为主上下文，避免跨单据跳转后场景丢失

## 页面范围

- `OrderDetailView`
- `ShipmentDetailView`
- `ReturnDetailView`
- `InvoiceDetailView`

## 关键设计

1. 构建统一异常提示块（触发条件/影响范围/恢复动作）。  
2. 在链路卡加入冲突检测提示（状态不一致、挂起节点）。  
3. 在关键动作后提供“下一步建议”与“回到主链路”入口。  

## 验证

- 页面交互回归（订单 -> 发运/退货/开票 -> 回链）
- `npx kse status --verbose`
- `npx kse doctor --docs`



## Ontology Model (Backfilled)

### Entities
- **PopcommerceNativeSceneClosureRecord**: Core domain record for Popcommerce Native Scene Closure scenarios.
- **PopcommerceNativeSceneClosureProcess**: Process context handling lifecycle transitions.
- **PopcommerceNativeSceneClosureAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **PopcommerceNativeSceneClosureRecord** 1:N **PopcommerceNativeSceneClosureProcess** (lifecycle orchestration).
- **PopcommerceNativeSceneClosureProcess** 1:N **PopcommerceNativeSceneClosureAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
