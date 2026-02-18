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

# 设计文档：PopCommerce 最终对齐（65-02）

## 设计重点

- 把“跨页面完成流程”提升为“页面内直接看懂链路”。
- 突出订单为中心的上下游状态总览。
- 保持现有 API 主体，优先补齐可视化与规则解释层。

## 关键设计

1. 订单详情增加履约链路卡（发运/退货/开票状态）。  
2. 发运与退货页面增加来源单据和后续影响提示。  
3. 统一异常提示与重试/回退交互语义。

## 验证

- 履约链路回归（下单 -> 发运 -> 退货 -> 结算）
- `npx kse status --verbose`
- `npx kse doctor --docs`

