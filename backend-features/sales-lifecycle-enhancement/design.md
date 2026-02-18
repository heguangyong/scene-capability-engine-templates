---
name: sales-lifecycle-enhancement
category: other
description: Template for Sales Lifecycle Enhancement
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：销售全生命周期增强（60-02）

## 设计要点

- 后端：增强 `OrderService/ShipmentService/InvoiceService/ReturnService`
- 前端：补齐订单审批、履约联动状态展示
- 集成：订单、发运、开票、退货状态机一致化

## 验收

- 下单到开票主流程可闭环
- 退货可关联原始销售链路
- 状态机与审计记录一致

