---
name: popcommerce-final-alignment
category: other
description: Template for Popcommerce Final Alignment
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：PopCommerce 最终对齐（65-02）

- [x] 1 梳理交易履约链路页面
  - [x] 1.1 盘点 `order/shipment/return-rma/invoice` 的链路断点
  - [x] 1.2 盘点跨单据跳转与状态回写断点

- [x] 2 补齐页面链路可见性
  - [x] 2.1 增加订单中心链路卡与上下游入口
  - [x] 2.2 增加发运/退货来源与影响提示

- [x] 3 补齐决策与异常体验
  - [x] 3.1 增加库存/MTO 决策条件展示
  - [x] 3.2 统一异常提示、重试与回退体验

- [x] 4 落证据与治理校验
  - [x] 4.1 更新 spec 文档与证据
  - [x] 4.2 执行 `status/doctor`
