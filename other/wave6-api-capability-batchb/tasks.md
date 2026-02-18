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

# Implementation Plan: Wave6 API 能力增强 Batch B（16-01）

## Tasks

- [x] 1 建立 Spec 基线
  - [x] 1.1 创建 `requirements.md`
  - [x] 1.2 创建 `design.md`
  - [x] 1.3 创建 `tasks.md`

- [x] 2 实现 API 发现与目录增强
  - [x] 2.1 增强 `ApiCatalogController#apiIndex` 分组/quickstart 结构（兼容旧字段）
  - [x] 2.2 增强 `ApiCatalogController#apiCatalog` summary/componentMatrix
  - [x] 2.3 增强 `ApiCatalogController#apiRoutes`（prefix/method 过滤与 methods 统计）

- [x] 3 同步 OpenAPI 与测试
  - [x] 3.1 在 `DocsController` 为 `/api/v1/routes` 补充 `prefix/method` 参数文档
  - [x] 3.2 新增 `ApiCatalogControllerWorkflowPropertyTest` 覆盖核心增强行为
  - [x] 3.3 扩展 `ServiceApiDocumentationPropertyTest` 验证新参数文档暴露

- [x] 4 验证与质量回采
  - [x] 4.1 执行新增测试定向验证
  - [x] 4.2 执行 `mvn -q -pl app -am test`
  - [x] 4.3 执行 `scripts/quality-baseline.ps1 -SpecSlug {{SPEC_NAME}}`

- [x] 5 KSE 收尾
  - [x] 5.1 执行 `kse status`
  - [x] 5.2 执行 `kse docs validate --all`
  - [x] 5.3 执行 `scripts/kse-continuity-check.ps1`
  - [x] 5.4 更新 `.kiro/steering/CURRENT_CONTEXT.md`
