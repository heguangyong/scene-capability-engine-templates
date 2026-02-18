---
name: wave6-coverage-batcha
category: other
description: Template for Wave6 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 覆盖率实施 Batch A（16-00）

## 概述

本 Spec 承接 `15-02`，聚焦 `ServiceInvocationService#validateParameters` 关键分支与静态辅助判断分支，目标是提升服务调用前置校验链路在异常与边界输入下的可回归性。

## 功能需求

### Requirement 1: 参数校验入口分支补测

**用户故事:** 作为项目维护者，我希望服务参数校验在不同上下文能力下行为可预测，以避免 API 层参数防线回归。

#### 验收标准

1. WHEN 执行上下文不满足深度校验前提 THEN 系统 SHALL 返回原始参数并保持可调用。
2. WHEN 服务定义不存在 THEN 系统 SHALL 返回 `RESOURCE_NOT_FOUND`（404）。
3. WHEN 参数存在未知字段 THEN 系统 SHALL 返回标准 `VALIDATION_ERROR`。

### Requirement 2: 参数清洗异常分支补测

**用户故事:** 作为项目维护者，我希望参数清洗阶段在业务错误和系统错误下具备稳定响应语义。

#### 验收标准

1. WHEN 参数转换后消息栈包含错误但无 validationErrors THEN 系统 SHALL 回退构建 details 列表。
2. WHEN 参数转换返回空结果 THEN 系统 SHALL 返回空 map 作为 cleaned 参数。
3. WHEN 参数转换抛 `IllegalArgumentException`/其他异常 THEN 系统 SHALL 分别返回 `VALIDATION_ERROR`（400）/`INTERNAL_ERROR`（500）。

### Requirement 3: 静态辅助方法边界补测

**用户故事:** 作为项目维护者，我希望服务调用内部静态判断在边界输入下行为稳定。

#### 验收标准

1. WHEN `isFailure` 输入为空或成功态 THEN 系统 SHALL 返回 `false`。
2. WHEN `extractHttpStatus` 输入结构不合法或状态非 Integer THEN 系统 SHALL 返回 `null`。
3. WHEN `extractHttpStatus` 输入合法整数状态 THEN 系统 SHALL 返回对应状态值。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望本批次推进具备完整可追踪证据。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

