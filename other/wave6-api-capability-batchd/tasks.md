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

# Implementation Plan: Wave6 API 能力增强 Batch D（16-03）

## Tasks

- [x] 1 建立 Spec 基线
  - [x] 1.1 创建 `requirements.md`
  - [x] 1.2 创建 `design.md`
  - [x] 1.3 创建 `tasks.md`

- [x] 2 实现组件维度路由目录能力
  - [x] 2.1 `/api/v1/routes` 增加 `component` 过滤与 `component` 字段
  - [x] 2.2 新增 `/api/v1/components/{componentName}/routes`
  - [x] 2.3 `/api/v1` 增加 `componentRoutesPattern` 导航字段

- [x] 3 同步 OpenAPI 与测试
  - [x] 3.1 `DocsController` 补充 routes 组件参数文档
  - [x] 3.2 扩展 `ApiCatalogControllerWorkflowPropertyTest`
  - [x] 3.3 扩展 `ServiceApiDocumentationPropertyTest` 与 `EndpointRegressionPropertyTest`

- [x] 4 验证与质量回采
  - [x] 4.1 执行定向测试
  - [x] 4.2 执行 `mvn -q -pl app -am test`
  - [x] 4.3 执行 `scripts/quality-baseline.ps1 -SpecSlug {{SPEC_NAME}}`

- [x] 5 KSE 收尾
  - [x] 5.1 执行 `kse status`
  - [x] 5.2 执行 `kse docs validate --all`
  - [x] 5.3 执行 `scripts/kse-continuity-check.ps1`
  - [x] 5.4 更新 `.kiro/steering/CURRENT_CONTEXT.md`
