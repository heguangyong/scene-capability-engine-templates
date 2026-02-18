---
name: wave6-api-capability-batchd
category: other
description: Template for Wave6 Api Capability Batchd
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch D（16-03）

## 设计目标

补齐“组件维度路由可发现性”：
- 全局 `/api/v1/routes` 支持按组件过滤并暴露 `component` 字段
- 提供 `/api/v1/components/{componentName}/routes` 组件路由目录
- docs 同步参数与路径

## 实施策略

1. **增强 `ApiCatalogController` 路由目录能力**
   - `apiRoutes()` 增加 `component` 过滤：
     - 通过 `inferComponent(path)` 计算路由组件归属
     - 每个路由项新增 `component` 字段
   - 新增 `componentRoutes()`：
     - 路由：`GET /api/v1/components/{componentName}/routes`
     - 内部复用全局路由扫描，默认仅返回组件专属路由
     - `includeGeneric=true` 时纳入通用模板路由（如 `/api/v1/components/{componentName}/...`）

2. **增强 `/api/v1` 导航信息**
   - 增加：
     - `componentRoutesPattern`: `/api/v1/components/{componentName}/routes`
     - 在 `componentHubs` 增加 `componentRoutesPattern`

3. **同步 `DocsController` 参数文档**
   - `/api/v1/routes` 增加 `component` 查询参数
   - `/api/v1/components/{componentName}/routes` 增加：
     - `includeGeneric`（boolean）
     - `tag`、`publicOnly`、`prefix`、`method`（与全局 routes 一致）

4. **测试策略**
   - 扩展 `ApiCatalogControllerWorkflowPropertyTest`：
     - 验证 `component` 字段存在与过滤生效
     - 验证组件路由目录端点行为（含 includeGeneric）
   - 扩展 `ServiceApiDocumentationPropertyTest`：
     - 验证新增路径与参数在 OpenAPI 可见
   - 扩展 `EndpointRegressionPropertyTest`：
     - 新增组件路由目录路径回归

## 风险与兼容性

- 所有新增参数均为可选，默认行为保持兼容。
- 组件过滤不依赖底层目录服务，仅基于路由路径推断，避免运行时耦合。

