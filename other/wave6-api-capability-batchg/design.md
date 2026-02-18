---
name: wave6-api-capability-batchg
category: other
description: Template for Wave6 Api Capability Batchg
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch G（16-06）

## 设计目标

在既有 routes/catalog/search 能力之上，增加“tag first”目录体验：
- 标签目录总览 `/api/v1/tags`
- 标签路由明细 `/api/v1/tags/{tag}/routes`

## 实施策略

1. **增强 `ApiCatalogController`**
   - 新增路由：
     - `GET /api/v1/tags`
     - `GET /api/v1/tags/{tag}/routes`
   - `apiIndex()` 新增：
     - `tags`
     - `tagRoutesPattern`

2. **实现标签目录 `apiTags()`**
   - 从 `collectRoutes()` 拉取全量路由。
   - 应用统一过滤（`component/publicOnly/method/prefix`）。
   - 统计 `tag -> routeCount` 并生成 links：
     - `/api/v1/tags/{tag}/routes`
   - 返回 summary：`totalTags`、`totalRoutes`。

3. **实现标签路由 `tagRoutes()`**
   - path 参数 `tag` 必填。
   - 先按 tag 过滤，再应用统一过滤参数。
   - 返回与 routes 一致的数据结构（`routes/totalCount/tags/methods/components`）并附加 `tag`。

4. **OpenAPI 与测试同步**
   - `DocsController` 增加 `/api/v1/tags` 与 `/api/v1/tags/{tag}/routes` 参数说明。
   - `ApiCatalogControllerWorkflowPropertyTest`：新增标签目录/标签路由断言。
   - `ServiceApiDocumentationPropertyTest`：新增路径/参数可见性断言。
   - `EndpointRegressionPropertyTest`：新增路径回归。

## 兼容性

- 全量新增，不破坏既有接口。
- 标签来源沿用 `inferTag(path)`，不引入额外数据源。

