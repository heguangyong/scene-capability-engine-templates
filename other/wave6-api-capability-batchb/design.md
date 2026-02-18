---
name: wave6-api-capability-batchb
category: other
description: Template for Wave6 Api Capability Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch B（16-01）

## 设计目标

在不破坏既有兼容性的前提下，增强 API 发现体验与路由检索能力：
- `/api/v1` 从“平铺链接列表”升级为“分组 + quickstart + 保持旧字段”
- `/api/v1/catalog` 提供聚合摘要与组件矩阵
- `/api/v1/routes` 支持 `prefix` 与 `method` 过滤，并输出 methods 统计
- OpenAPI 与控制器能力同步，确保 `/docs`、`/redoc` 可见新增参数

## 实施策略

1. **增强 `ApiCatalogController`**
   - `apiIndex()`：在现有字段基础上增加 `groups`、`quickstart`、`componentHubs`、`serviceHubs`、`entityHubs`、`screenHubs`。
   - `apiCatalog()`：新增 `summary` 与 `componentMatrix`。
     - `summary` 聚合各目录 `total*` 字段。
     - `componentMatrix` 以组件名为键汇总 service/entity/screen 数量。
   - `apiRoutes()`：新增查询参数
     - `prefix`：按 path 前缀过滤（大小写不敏感）
     - `method`：按 HTTP 方法过滤（统一大写比对）
     - 返回 `methods`（去重排序）

2. **对齐 `DocsController` 文档参数**
   - 在 `/api/v1/routes` 的 OpenAPI 查询参数中新增：
     - `prefix`: Filter routes by path prefix
     - `method`: Filter routes by HTTP method

3. **新增控制器级测试**
   - 新增 `ApiCatalogControllerWorkflowPropertyTest`（反射调用私有方法 + Stub RequestContext）：
     - `apiIndex` 结构增强且兼容字段保留
     - `apiCatalog` summary/componentMatrix 计算
     - `apiRoutes` 过滤（tag/publicOnly/prefix/method）与统计字段
   - 扩展 `ServiceApiDocumentationPropertyTest`：验证 OpenAPI 对 `/api/v1/routes` 的 `prefix` 与 `method` 参数暴露。

4. **验证策略**
   - 定向测试：`ApiCatalogControllerWorkflowPropertyTest` + `ServiceApiDocumentationPropertyTest`
   - 全量测试：`mvn -q -pl app -am test`
   - 质量基线：`scripts/quality-baseline.ps1 -SpecSlug {{SPEC_NAME}}`

## 兼容性与风险控制

- 保留 `/api/v1` 的原平铺字段（`docs/openapi/services/entities...`）避免客户端破坏性升级。
- `/api/v1/routes` 新参数为可选，默认行为与现状一致。
- `componentMatrix` 基于已有目录返回结构做只读聚合，不变更底层目录服务契约。
