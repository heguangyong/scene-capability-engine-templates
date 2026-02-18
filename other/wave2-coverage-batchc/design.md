---
name: wave2-coverage-batchc
category: other
description: Template for Wave2 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave2 覆盖率实施 Batch C（12-06）

## 设计目标

针对 `ServiceInvocationService` 的事务重路径做低依赖、可重复的分支覆盖，重点验证事务元信息（`_tx`）与错误语义的一致性。

## 实施策略

1. **构建 lightweight 代理桩（Proxy-based stubs）**
   - 使用 `Proxy.newProxyInstance` 构造：
     - `ExecutionContextFactory`
     - `ExecutionContext`
     - `TransactionFacade`
     - `ServiceFacade`
     - `ServiceCallSync`
     - `MessageFacade`
   - 通过可配置行为驱动不同事务分支（成功/客户端错误/事务异常/服务异常）。

2. **覆盖 `runWithUseOrBegin` 分支**
   - `begin -> call -> commit` 成功路径。
   - `begin -> call(failure 4xx) -> rollback` 客户端错误路径。
   - `begin -> call throws IllegalArgumentException -> rollback` 参数校验异常路径。

3. **覆盖 `runWithRequireNew` 分支**
   - `isTransactionInPlace=true -> suspend -> begin -> call -> commit -> resume`。
   - `call throws IllegalArgumentException -> rollback -> VALIDATION_ERROR`。
   - `call returns SERVICE_ERROR(4xx) -> rollback -> rolled-back-client-error`。

## 验证策略

1. 定向运行新增测试类。
2. 运行 `mvn -q -pl app -am test`。
3. 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `12-05` 对比报告。
