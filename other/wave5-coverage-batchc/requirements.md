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

# Requirements: Wave5 覆盖率实施 Batch C（15-02）

## 概述

本 Spec 承接 `15-01`，聚焦 `ServiceInvocationService` 在异步兼容路径、任务状态回查回退路径与事务异常分支下的稳定性，目标是强化服务调用层关键边界行为。

## 功能需求

### Requirement 1: 异步兼容调用分支补测

**用户故事:** 作为项目维护者，我希望旧版异步调用入口在合法/非法输入与异常场景下有可回归行为，以避免历史兼容路径回归。

#### 验收标准

1. WHEN `invokeAsyncLegacy` 输入合法服务名 THEN 系统 SHALL 创建任务记录并返回 `PENDING`。
2. WHEN `invokeAsyncLegacy` 服务名非法 THEN 系统 SHALL 返回 `VALIDATION_ERROR`（400）。
3. WHEN `invokeAsyncLegacy` 任务落库阶段抛异常 THEN 系统 SHALL 返回 `INTERNAL_ERROR`（500）。

### Requirement 2: 任务状态查询回退分支补测

**用户故事:** 作为项目维护者，我希望任务状态查询在内存命中与实体回退两条路径下都保持稳定。

#### 验收标准

1. WHEN `getJobStatus` 命中 in-memory tracker THEN 系统 SHALL 直接返回任务状态。
2. WHEN in-memory 未命中且实体存在 THEN 系统 SHALL 返回实体任务状态数据。
3. WHEN in-memory 未命中且实体不存在 THEN 系统 SHALL 返回 `RESOURCE_NOT_FOUND`（404）。

### Requirement 3: 事务失败分支补测

**用户故事:** 作为项目维护者，我希望 API 事务封装在服务失败与事务异常下具备可验证回滚语义。

#### 验收标准

1. WHEN `use-or-begin` 下服务返回 5xx 失败结果 THEN 系统 SHALL 标记 `rolled-back-error`。
2. WHEN `require-new` 下事务 begin 抛 `TransactionException` THEN 系统 SHALL 返回 `TRANSACTION_ERROR`。
3. WHEN `require-new` 恢复挂起事务失败 THEN 系统 SHALL 不破坏主流程响应语义。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望本批次推进具备完整可追踪证据。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

