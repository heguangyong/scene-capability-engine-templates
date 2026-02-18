---
name: production-planning-mrp
category: other
description: Template for Production Planning Mrp
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：生产计划与 MRP 增强（60-03）

## 设计要点

- 新增计划服务层：需求、MRP、计划订单、异常消息
- 对接 `BomService/RoutingService/InventoryService/ProcurementService`
- 前端增加计划看板与异常处理视图

## 验收

- 支持一键 MRP 计算并输出计划订单
- 可从计划订单转生产/采购单
- 产能负载基础指标可查询

