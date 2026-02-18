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

