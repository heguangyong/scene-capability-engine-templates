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

# 需求文档：PopCommerce 原生场景闭环（66-02）

## 概述

围绕 `Order -> Shipment -> Return -> Invoice` 主链，进一步补齐 Moqui 原生履约语义：

1. 统一异常处理与恢复表达；
2. 强化跨单据链路的状态一致性提示；
3. 强化链路动作与决策依据可见性。

## 需求 1：履约异常语义统一

### 验收标准

1.1 订单/发运/退货/开票页面异常提示结构一致  
1.2 提供统一重试、回退、人工介入建议  
1.3 异常操作后可追溯影响单据

## 需求 2：链路状态一致性可见

### 验收标准

2.1 关键状态冲突可被页面明确提示  
2.2 上下游单据状态联动关系可见  
2.3 决策依据（库存/审批/结算）可见

## 需求 3：治理与证据闭环

### 验收标准

3.1 变更可映射 ontology 三层  
3.2 `status/doctor` 通过  
3.3 证据落盘并同步总控 spec

