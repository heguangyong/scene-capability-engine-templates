---
name: wave5-coverage-batcha
category: other
description: Template for Wave5 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: Wave5 覆盖率实施 Batch A（15-00）

## Tasks

- [x] 1 建立 Spec 基线
  - [x] 1.1 创建 `requirements.md`
  - [x] 1.2 创建 `design.md`
  - [x] 1.3 创建 `tasks.md`

- [x] 2 补充 EntityCatalog/EntityCrud 深分支测试
  - [x] 2.1 扩展 `EntityCatalogServiceSnapshotWorkflowPropertyTest` 覆盖 listRelationships 分支
  - [x] 2.2 扩展 `EntityCrudServiceBatchWorkflowPropertyTest` 覆盖 operations 类型/上限与 action/entityId 别名
  - [x] 2.3 扩展 `EntityCrudServiceWorkflowPropertyTest` 覆盖 listRelated 前置校验分支

- [x] 3 验证与覆盖率回采
  - [x] 3.1 执行新增测试定向验证
  - [x] 3.2 执行 `mvn -q -pl app -am test`
  - [x] 3.3 执行 `scripts/quality-baseline.ps1` 并记录与 14-02 增量

- [x] 4 KSE 收尾
  - [x] 4.1 执行 `kse status`
  - [x] 4.2 执行 `kse docs validate --all`
  - [x] 4.3 更新 `.kiro/steering/CURRENT_CONTEXT.md`
