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

# Requirements: Wave2 覆盖率实施 Batch C（12-06）

## 概述

本 Spec 承接 `12-05`，聚焦 `ServiceInvocationService` 的事务重路径，优先覆盖：

- `runWithUseOrBegin`
- `runWithRequireNew`

目标是在不依赖真实 Moqui runtime 的前提下，补齐事务分支控制流测试，降低 API 事务语义回归风险。

## 功能需求

### Requirement 1: use-or-begin 事务分支补测

**用户故事:** 作为项目维护者，我希望 `use-or-begin` 策略在提交、客户端错误回滚与参数校验失败场景下行为可验证，以确保 API 层事务封装稳定。

#### 验收标准

1. WHEN 使用 `use-or-begin` 且服务成功 THEN 系统 SHALL 提交事务并返回 `committed`。
2. WHEN 使用 `use-or-begin` 且服务返回客户端错误 THEN 系统 SHALL 回滚并返回 `rolled-back-client-error`。
3. WHEN 使用 `use-or-begin` 且服务调用抛出参数校验异常 THEN 系统 SHALL 回滚并返回 `VALIDATION_ERROR`。

### Requirement 2: require-new 事务分支补测

**用户故事:** 作为项目维护者，我希望 `require-new` 策略在挂起/恢复、参数校验失败与客户端错误回滚场景下行为可验证，以保证独立事务语义可追踪。

#### 验收标准

1. WHEN 使用 `require-new` 且已有事务 THEN 系统 SHALL 覆盖挂起、提交、恢复流程。
2. WHEN 使用 `require-new` 且发生参数校验失败 THEN 系统 SHALL 回滚并返回 `VALIDATION_ERROR`。
3. WHEN 使用 `require-new` 且服务报告客户端错误 THEN 系统 SHALL 回滚并返回 `rolled-back-client-error`。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望持续按 KSE 更新任务和上下文，保证跨会话可恢复推进。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。
