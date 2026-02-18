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

# Design: Wave5 覆盖率实施 Batch A（15-00）

## 设计目标

通过扩展现有工作流测试与新增边界测试，覆盖 `EntityCatalogService` 与 `EntityCrudService` 复杂分支的高风险路径。

## 实施策略

1. **扩展 `EntityCatalogServiceSnapshotWorkflowPropertyTest`**
   - 新增 `listRelationships()` 对定义缺失、关系缺省、有关系数据三种路径的验证。

2. **扩展 `EntityCrudServiceBatchWorkflowPropertyTest`**
   - 补充 `operations` 非数组、超限场景。
   - 补充 `action/entityId` 别名路径，验证 batch 分支兼容性。

3. **扩展 `EntityCrudServiceWorkflowPropertyTest`**
   - 增加 `listRelated` 对非法 `fields/orderBy/filter` 的前置校验分支验证（确保运行时未被访问）。

4. **验证策略**
   - 定向运行 `EntityCatalogServiceSnapshotWorkflowPropertyTest`、`EntityCrudServiceBatchWorkflowPropertyTest`、`EntityCrudServiceWorkflowPropertyTest`。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `14-02` 增量报告。

