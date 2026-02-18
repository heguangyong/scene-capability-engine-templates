---
name: wave6-api-capability-batchf
category: other
description: Template for Wave6 Api Capability Batchf
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch F（16-05）

## 设计目标

新增“搜索即入口”能力，降低 API 查找成本：
- 全局搜索 `/api/v1/search`
- 组件搜索 `/api/v1/components/{componentName}/search`
- 导航与 OpenAPI 同步增强

## 实施策略

1. **增强 `ApiCatalogController`**
   - 注册新增端点：
     - `GET /api/v1/search`
     - `GET /api/v1/components/{componentName}/search`
   - `apiIndex()` 新增：
     - `search`: `/api/v1/search`
     - `componentSearchPattern`: `/api/v1/components/{componentName}/search`

2. **实现全局搜索 `apiSearch()`**
   - 必填参数：`q`
   - 可选参数：`component`、`size`（每类结果返回上限）
   - 数据来源：
     - services：`ServiceCatalogService.listAllServices(component,q,0,size,false)`
     - entities：`EntityCatalogService.listEntities(component,null,q,0,size)`
     - screens：`ScreenCatalogService.listScreens(component,q,0,size)`
     - routes：基于路由扫描与通用过滤，匹配 path/tag/method/component
   - 返回结构：
     - `query` / `component` / `size`
     - `services/entities/screens/routes`
     - `summary`（各类匹配数 + `totalMatches`）

3. **实现组件搜索 `componentSearch()`**
   - 组件名来自 path，内部复用 `performSearch()` 逻辑。
   - 统一错误语义：
     - `q` 缺失 → 400
     - 组件非法/不存在 → 404（通过目录服务探测）

4. **OpenAPI 与测试同步**
   - `DocsController` 为新增路径补充 query/path 参数。
   - 测试扩展：
     - `ApiCatalogControllerWorkflowPropertyTest`：搜索结果结构与错误语义。
     - `ServiceApiDocumentationPropertyTest`：路径与参数可见性。
     - `EndpointRegressionPropertyTest`：新增路径可路由。

## 兼容性

- 仅新增入口与字段，不破坏现有路径。
- 搜索默认 `size=10` 并限制最大值，避免返回过大 payload。

