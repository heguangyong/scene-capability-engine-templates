---
name: wave4-coverage-batcha
category: other
description: Template for Wave4 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave4 覆盖率实施 Batch A（14-00）

## 概述

本 Spec 承接 `13-02`，聚焦 `ServiceController` 在组件别名入口、组件归属校验、目录缺失与参数解析分支，目标是提升服务 API 入口层的稳定性与回归可验证性。

## 功能需求

### Requirement 1: 组件别名与目录入口分支补测

**用户故事:** 作为项目维护者，我希望 `/framework`、`/mantle-usl`、`/mantle-udm` 及全局目录入口在目录可用/不可用时行为可预测，以避免上线后出现不可诊断的入口回归。

#### 验收标准

1. WHEN `serviceCatalog` 未初始化 THEN 系统 SHALL 对目录依赖型入口返回 `503 SERVICE_UNAVAILABLE`。
2. WHEN `listAllServices` 参数导致目录层抛出 `IllegalArgumentException` THEN 系统 SHALL 返回 `400 VALIDATION_ERROR`。
3. WHEN `service-hub` 被访问 THEN 系统 SHALL 返回 docs/openapi/components/groups/globalServices 的稳定结构。

### Requirement 2: 服务定义与调用分支补测

**用户故事:** 作为项目维护者，我希望按组件/按路径/按别名调用时，归属校验与不存在分支都能稳定响应，避免服务入口语义漂移。

#### 验收标准

1. WHEN 服务不属于目标组件 THEN 系统 SHALL 返回 `404 RESOURCE_NOT_FOUND`。
2. WHEN 组件归属通过但服务详情缺失 THEN 系统 SHALL 返回 `404 RESOURCE_NOT_FOUND`。
3. WHEN 固定组件入口满足归属并通过校验 THEN 系统 SHALL 调用同步服务并返回 `200`。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进都具备完整可追踪证据，以避免会话中断导致上下文断裂。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

