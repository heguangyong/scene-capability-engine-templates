---
name: wave4-coverage-batchc
category: other
description: Template for Wave4 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave4 覆盖率实施 Batch C（14-02）

## 设计目标

通过扩展现有属性测试与快照工作流测试，补齐 `EntityCatalogService` 与 `EntityCrudService` 的低成本高收益分支覆盖。

## 实施策略

1. **扩展 `EntityCatalogServiceInternalsPropertyTest`**
   - 通过反射补测 `inferComponentFromPackage()` 推断分支。
   - 补测 `joinLocation()` 与 `firstNonBlank()` 的边界行为。

2. **扩展 `EntityCatalogServiceSnapshotWorkflowPropertyTest`**
   - 增加未知组件但格式合法时的空结果结构测试。
   - 增加分页归一化（负 pageIndex、超大 pageSize）测试。

3. **新增 `EntityCrudServiceParsingPropertyTest`**
   - 聚焦纯静态解析：`parseFieldList` / `parseOrderByList` / `sanitizeFilterParams`。
   - 不依赖 Moqui runtime，保证执行速度与稳定性。

4. **验证策略**
   - 定向运行 `EntityCatalogService*` 与 `EntityCrudServiceParsingPropertyTest`。
   - 运行 `mvn -q -pl app -am test` 全链路回归。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `14-01` 增量报告。



## Ontology Model (Backfilled)

### Entities
- **Wave4CoverageBatchcRecord**: Core domain record for Wave4 Coverage Batchc scenarios.
- **Wave4CoverageBatchcProcess**: Process context handling lifecycle transitions.
- **Wave4CoverageBatchcAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **Wave4CoverageBatchcRecord** 1:N **Wave4CoverageBatchcProcess** (lifecycle orchestration).
- **Wave4CoverageBatchcProcess** 1:N **Wave4CoverageBatchcAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
