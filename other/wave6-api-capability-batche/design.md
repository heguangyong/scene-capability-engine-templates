---
name: wave6-api-capability-batche
category: other
description: Template for Wave6 Api Capability Batche
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch E（16-04）

## 设计目标

围绕“组件为中心”的 API 可发现性再提升：
- 新增组件导航中心（hub）
- 新增单组件聚合入口（overview）
- 统一组件目录 links 字段，形成稳定导航图谱

## 实施策略

1. **增强 `ApiCatalogController` 路由与导航**
   - 注册新增端点：
     - `GET /api/v1/components/hub`
     - `GET /api/v1/components/{componentName}`
   - `apiIndex()` 增加：
     - `componentsHub`
     - `componentOverviewPattern`

2. **实现组件导航中心 `componentsHub()`**
   - 聚合 `serviceCatalog.listComponents()`、`entityCatalog.listComponents()`、`screenCatalog.listComponents()`。
   - 复用 `buildComponentMatrix()` 生成组件矩阵。
   - 为每行组件填充统一 links：
     - `overview`、`catalog`、`routes`、`services`、`entities`、`screens`
   - 返回导航 pattern 与 summary。

3. **实现单组件聚合入口 `componentOverview()`**
   - 通过轻量查询获得该组件 service/entity/screen 计数。
   - 通过路由收集逻辑统计该组件 routeCount（支持 `includeGenericRoutes` 参数）。
   - 返回 links：
     - `overview`、`catalog`、`routes`、`services`、`entities`、`screens`
   - 参数设计：
     - `includeSamples`、`sampleSize`（与 component catalog 对齐）
     - `includeGenericRoutes`（控制 routes 统计是否纳入模板路由）

4. **组件目录 links 一致性增强**
   - `componentsCatalog()` 每行 links 增加：`overview`、`routes`。
   - `componentCatalog()` links 增加：`overview`、`routes`。

5. **OpenAPI 与测试同步**
   - `DocsController` 为新增路径补充参数说明。
   - 扩展测试：
     - `ApiCatalogControllerWorkflowPropertyTest`：新增 hub/overview/links 一致性断言。
     - `ServiceApiDocumentationPropertyTest`：新增路径与参数可见性断言。
     - `EndpointRegressionPropertyTest`：新增路径可路由性。

## 兼容性与风险

- 全部为新增路径或新增返回字段，不影响现有调用。
- `componentOverview` 复用现有 catalog service 与路由扫描，不引入额外存储状态。
- 对不存在组件仍返回 404，行为与既有 `componentCatalog` 保持一致。

