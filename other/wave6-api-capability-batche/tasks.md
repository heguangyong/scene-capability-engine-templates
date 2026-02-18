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

# Implementation Plan: Wave6 API 能力增强 Batch E（16-04）

## Tasks

- [x] 1 建立 Spec 基线
  - [x] 1.1 创建 `requirements.md`
  - [x] 1.2 创建 `design.md`
  - [x] 1.3 创建 `tasks.md`

- [x] 2 实现组件导航与聚合入口
  - [x] 2.1 新增 `GET /api/v1/components/hub`
  - [x] 2.2 新增 `GET /api/v1/components/{componentName}`
  - [x] 2.3 增强 `/api/v1` 导航字段（`componentsHub`/`componentOverviewPattern`）

- [x] 3 增强组件目录 links 一致性
  - [x] 3.1 `componentsCatalog` links 增加 `overview/routes`
  - [x] 3.2 `componentCatalog` links 增加 `overview/routes`

- [x] 4 同步 OpenAPI 与测试
  - [x] 4.1 `DocsController` 增加 hub/overview 路径与参数说明
  - [x] 4.2 扩展 `ApiCatalogControllerWorkflowPropertyTest`
  - [x] 4.3 扩展 `ServiceApiDocumentationPropertyTest` 与 `EndpointRegressionPropertyTest`

- [x] 5 验证与质量回采
  - [x] 5.1 执行新增测试定向验证
  - [x] 5.2 执行 `mvn -q -pl app -am test`
  - [x] 5.3 执行 `scripts/quality-baseline.ps1 -SpecSlug {{SPEC_NAME}}`

- [x] 6 KSE 收尾
  - [x] 6.1 执行 `kse status`
  - [x] 6.2 执行 `kse docs validate --all`
  - [x] 6.3 执行 `scripts/kse-continuity-check.ps1`
  - [x] 6.4 更新 `.kiro/steering/CURRENT_CONTEXT.md`
