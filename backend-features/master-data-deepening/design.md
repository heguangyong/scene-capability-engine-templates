---
name: master-data-deepening
category: other
description: Template for Master Data Deepening
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：主数据深能力（60-01）

## 设计要点

- 后端：扩展 `ProductService/BomService/RoutingService` 的字段与校验
- 前端：补齐对应表单与详情页高级字段
- API：保持 REST 风格并更新 OpenAPI schema

## 验收

- 主数据 CRUD 含新增字段
- BOM 变体/替代件规则可读可写
- 路由与物料绑定关系可查询

