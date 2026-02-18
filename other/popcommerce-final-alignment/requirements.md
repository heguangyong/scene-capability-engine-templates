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

# 需求文档：PopCommerce 最终对齐（65-02）

## 概述

围绕 `Product -> Order -> Shipment -> Return -> Invoice` 主链，补齐交易履约场景的一致性、链路可见性与异常处理体验。

## 需求 1：交易履约链路一屏可追踪

### 验收标准

1.1 订单页可见发运/退货/开票当前状态  
1.2 可在关键页面快速跳转到上下游单据  
1.3 异常状态具备明确修复建议

## 需求 2：规则与决策可见性增强

### 验收标准

2.1 MTO/MTS 与库存决策条件可见  
2.2 退货与库存回滚规则可见  
2.3 关键履约规则有统一提示风格

## 需求 3：治理与证据闭环

### 验收标准

3.1 关键页面改造有证据文档  
3.2 `status/doctor` 通过  
3.3 ontology gate 不退化

