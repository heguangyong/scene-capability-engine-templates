---
name: marbleerp-final-alignment
category: other
description: Template for Marbleerp Final Alignment
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：MarbleERP 最终对齐（65-03）

## 概述

围绕 `Demand -> MRP -> Production/Procurement -> Inventory -> Costing` 主链，补齐计划执行核算场景的决策可见性与页面闭环组织。

## 需求 1：计划执行核算链路可解释

### 验收标准

1.1 MRP 页面可展示关键决策输出与去向  
1.2 工单/采购/库存/成本页面可展示来源关系  
1.3 异常重排与审批链路可回溯

## 需求 2：复杂业务页面可读性与可操作性增强

### 验收标准

2.1 高密度表格/表单可读性与重点信息突出  
2.2 关键操作前后状态变化可见  
2.3 跨模块跳转路径稳定且语义一致

## 需求 3：治理与证据闭环

### 验收标准

3.1 变更落入 scene 与审计证据  
3.2 `status/doctor` 通过  
3.3 ontology gate 不退化

