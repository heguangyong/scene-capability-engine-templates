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

# 设计文档：MarbleERP 最终对齐（65-03）

## 设计重点

- 将复杂链路中的“决策输入/输出”显式化。
- 强化计划与执行、执行与核算之间的页面连接语义。
- 优先提升场景表达质量，避免继续堆叠孤立页面能力。

## 关键设计

1. MRP 页面增加决策来源与建议去向视图。  
2. 生产/采购/库存/成本页面补充来源追踪与影响提示。  
3. 为异常处理提供标准化恢复路径提示。

## 验证

- 链路回归（需求 -> MRP -> 执行 -> 库存/成本）
- `npx kse status --verbose`
- `npx kse doctor --docs`

