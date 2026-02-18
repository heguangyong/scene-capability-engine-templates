---
name: wave5-coverage-batchc
category: other
description: Template for Wave5 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave5 覆盖率实施 Batch C（15-02）

## 设计目标

在不引入重型运行时依赖的前提下，通过动态代理与反射补测 `ServiceInvocationService` 的异步兼容路径、任务回查回退路径与事务异常路径，提升服务调用层分支稳定性。

## 实施策略

1. **扩展 `ServiceInvocationServiceWorkflowPropertyTest`**
   - 新增 `getJobStatus` 回退至实体查询的成功/404 分支测试。
   - 新增 `invokeAsyncLegacy` 正常、服务名校验失败、任务创建异常分支测试。
   - 通过轻量 `ExecutionContext`/`EntityFacade`/`EntityValue`/`ServiceFacade` 代理模拟行为。

2. **扩展 `ServiceInvocationServiceTransactionWorkflowPropertyTest`**
   - 新增 `use-or-begin` 下 5xx 失败回滚分支断言（`rolled-back-error`）。
   - 新增 `require-new` 下 `begin` 抛 `TransactionException` 分支断言（`TRANSACTION_ERROR`）。
   - 新增 `require-new` `resume` 抛异常但主结果保持成功的分支断言。

3. **验证策略**
   - 定向运行本批次两组测试。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `15-01` 的增量报告。



## Ontology Model (Backfilled)

### Entities
- **Wave5CoverageBatchcRecord**: Core domain record for Wave5 Coverage Batchc scenarios.
- **Wave5CoverageBatchcProcess**: Process context handling lifecycle transitions.
- **Wave5CoverageBatchcAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **Wave5CoverageBatchcRecord** 1:N **Wave5CoverageBatchcProcess** (lifecycle orchestration).
- **Wave5CoverageBatchcProcess** 1:N **Wave5CoverageBatchcAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
