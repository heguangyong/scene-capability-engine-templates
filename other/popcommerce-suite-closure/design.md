---
name: popcommerce-suite-closure
category: other
description: Template for Popcommerce Suite Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：PopCommerce 套件闭环（64-02）

## 1. 目标

- 对齐 PopCommerce 的“商品 + 订单 + 履约 + 退货”语义到 331 领域导航。
- 输出可被 KSE 复用的场景合同与模板资产。

## 2. 组织策略

- 入口模块：`product`、`order`、`shipment`、`return-rma`、`inventory`
- 关键路由：`/product`、`/order`、`/shipment`、`/return-rma`
- 关联总线：`31-00`、`32-00`、`35-00`、`51-00`、`62-03`

## 3. ontology 三层

1. 实体关系：`Product -> Order -> Shipment -> Return -> Invoice`
2. 业务规则：下单校验、库存校验、履约状态机、退货闭环
3. 决策逻辑：按库存与订单类型分流 MTO/MTS 与补货/回退

## 4. 输出

- `docs/popcommerce-suite-map.md`
- `docs/scene.yaml`
- `docs/scene-package.json`

