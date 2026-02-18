---
name: wave2-coverage-batcha
category: other
description: Template for Wave2 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave2 覆盖率实施 Batch A（12-04）

## 概述

本 Spec 承接 Wave1，进入 Wave2。目标是覆盖 `EntityCrudService` 与 `ServiceInvocationService` 的高指令密度业务路径，争取可见覆盖率提升。

## 功能需求

### Requirement 1: EntityCrudService 批处理路径补测

**用户故事:** 作为项目维护者，我希望验证批处理路径的关键分支，以降低批量操作变更风险。

#### 验收标准

1. WHEN 传入空 body 或空 operations THEN 系统 SHALL 返回标准化 `VALIDATION_ERROR`。
2. WHEN operation 项非法或中途失败 THEN 系统 SHALL 覆盖 `abort` 与 `continueOnError` 分支。
3. WHEN create/update/delete 返回成功/失败/异常 THEN 系统 SHALL 生成一致的 operation 结果与 summary。

### Requirement 2: ServiceInvocationService 主路径补测

**用户故事:** 作为项目维护者，我希望验证事务策略与异步任务主路径，以确保服务调用链行为稳定。

#### 验收标准

1. WHEN `invokeSyncWithTransaction` 在 `service` 策略运行 THEN 系统 SHALL 返回含事务元信息的结果。
2. WHEN `invokeSyncWithTransaction` 捕获非法参数异常 THEN 系统 SHALL 返回 `VALIDATION_ERROR`。
3. WHEN `invokeAsync` 执行成功/失败 THEN 系统 SHALL 记录任务状态并可通过 `getJobStatus` 查询。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望 Wave2 执行过程持续可追踪，以避免会话切换带来的推进断裂。

#### 验收标准

1. WHEN 子任务推进 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

