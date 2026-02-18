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

# 需求文档：MarbleERP 原生场景闭环（66-03）

## 概述

围绕 `MRP -> Production/Procurement -> Inventory -> Cost` 主链，进一步补齐原生场景决策闭环：

1. 强化决策结果在执行页可追踪；
2. 强化执行异常到成本影响的解释；
3. 强化跨模块恢复路径可操作性。

## 需求 1：决策结果可追踪

### 验收标准

1.1 MRP 决策输出在执行页可见  
1.2 执行状态可回溯到决策来源  
1.3 关键分支（自制/外购/转储）有明确去向

## 需求 2：异常影响可解释

### 验收标准

2.1 执行异常对库存/成本影响可见  
2.2 异常恢复路径（重排/回退/审批）可见  
2.3 成本偏差可追溯到上游执行节点

## 需求 3：治理与证据闭环

### 验收标准

3.1 变更可映射 ontology 三层  
3.2 `status/doctor` 通过  
3.3 证据落盘并同步总控 spec

