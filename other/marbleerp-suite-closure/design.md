---
name: marbleerp-suite-closure
category: other
description: Template for Marbleerp Suite Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：MarbleERP 套件闭环（64-03）

## 1. 目标

- 对齐 MarbleERP 的“计划驱动 + 运营协同 + 治理收口”语义到 331 领域导航。
- 输出可被 KSE 内化与多 agent 编排消费的场景合同。

## 2. 组织策略

- 入口模块：`mrp-planning`、`production-run`、`procurement`、`inventory`、`equipment`、`cost`、`workflow-approval`、`reporting-ops`
- 关键路由：`/mrp-planning`、`/production-run`、`/procurement`、`/cost`
- 关联总线：`60-01~60-12`、`62-02`、`62-03`、`63-01~63-03`

## 3. ontology 三层

1. 实体关系：`Demand -> MRP -> Production/Procurement -> Inventory -> Costing`
2. 业务规则：计划策略、工单状态机、采购库存核算一致性
3. 决策逻辑：按供需缺口分流自制/外购/转储，异常触发重排与审批

## 4. 输出

- `docs/marbleerp-suite-map.md`
- `docs/scene.yaml`
- `docs/scene-package.json`

