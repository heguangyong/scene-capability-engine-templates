---
name: wave6-api-capability-batchc
category: other
description: Template for Wave6 Api Capability Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 API 能力增强 Batch C（16-02）

## 设计目标

在既有 `/api/v1`、`/api/v1/catalog`、`/api/v1/routes` 基础上，补齐组件视角的 API 目录治理：
- 提供组件总览目录（矩阵 + 跳转链接）
- 提供单组件聚合目录详情（支持示例条目）
- 与 OpenAPI 及 `/api/v1` 入口联动，确保 docs 可发现

## 实施策略

1. **增强 `ApiCatalogController`**
   - 注册新增路由：
     - `GET /api/v1/components/catalog`
     - `GET /api/v1/components/{componentName}/catalog`
   - `componentsCatalog()`：
     - 复用 service/entity/screen 三类 catalog 数据
     - 输出 `components` 列表（counts + totalApiItems + links）
     - 输出 `summary`（totalComponents + totals）
   - `componentCatalog()`：
     - 校验组件存在性
     - 输出组件聚合 counts + links
     - 可选 `includeSamples=true` 时返回示例条目（默认关闭）
     - `sampleSize` 控制示例数量（默认 3，范围 1~20）

2. **增强 `/api/v1` 入口可见性**
   - 在 `apiIndex` 中增加 `componentsCatalog` 与 `componentCatalogPattern` 导航字段

3. **同步 `DocsController` OpenAPI 参数**
   - 为 `/api/v1/components/{componentName}/catalog` 添加查询参数：
     - `includeSamples`（boolean）
     - `sampleSize`（integer）

4. **测试策略**
   - 扩展 `ApiCatalogControllerWorkflowPropertyTest`：
     - 覆盖 `componentsCatalog` 正常与 503 分支
     - 覆盖 `componentCatalog` 正常、404、sample 参数边界
   - 扩展 `ServiceApiDocumentationPropertyTest`：
     - 校验新增路径存在
     - 校验 `includeSamples`/`sampleSize` 参数可见
   - 扩展 `EndpointRegressionPropertyTest`：
     - 增加新增端点可达性基线

## 兼容性与风险控制

- 所有新增路由均为增量，不破坏现有路径。
- 默认 `includeSamples=false`，避免目录响应体过大。
- 组件不存在时返回 404，避免空数据误导。

