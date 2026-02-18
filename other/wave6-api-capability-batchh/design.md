---
name: wave6-api-capability-batchh
category: other
description: Template for Wave6 Api Capability Batchh
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch H（16-07）

## 设计目标

在 routes/tag 目录能力基础上新增 method first 导航：
- `GET /api/v1/methods`
- `GET /api/v1/methods/{method}/routes`

## 实施策略

1. **增强 `ApiCatalogController`**
   - 新增路由注册：
     - `/api/v1/methods`
     - `/api/v1/methods/{method}/routes`
   - `apiIndex()` 新增：
     - `methods`
     - `methodRoutesPattern`

2. **实现方法目录 `apiMethods()`**
   - 基于 `collectRoutes()` 获取路由。
   - 支持过滤：`component/publicOnly/tag/prefix`。
   - 统计 `method -> routeCount`，生成 links：
     - `/api/v1/methods/{method}/routes`
   - 返回 summary：`totalMethods`、`totalRoutes`。

3. **实现方法路由 `methodRoutes()`**
   - path 参数 `method` 统一 normalize 为大写。
   - 先按 method 过滤，再应用过滤参数。
   - 返回 `routeResponse` 结构，并附加 `method`。

4. **OpenAPI 与测试同步**
   - `DocsController` 增加方法目录路径与参数文档。
   - `ApiCatalogControllerWorkflowPropertyTest`：新增目录/路由断言。
   - `ServiceApiDocumentationPropertyTest`：新增路径/参数可见性断言。
   - `EndpointRegressionPropertyTest`：新增路由回归。

## 兼容性

- 新增能力，不影响既有路径。
- method 大小写统一处理，减少调用歧义。

