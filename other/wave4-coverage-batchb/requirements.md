---
name: wave4-coverage-batchb
category: other
description: Template for Wave4 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave4 覆盖率实施 Batch B（14-01）

## 概述

本 Spec 承接 `14-00`，聚焦 `ServiceCatalogService` 的目录查询与组件解析分支，目标是提升服务目录 API 的容错与契约稳定性。

## 功能需求

### Requirement 1: 目录查询异常分支补测

**用户故事:** 作为项目维护者，我希望服务目录在未知组件、空白参数、非法查询条件下行为稳定，以避免目录入口在生产中出现不可预期返回。

#### 验收标准

1. WHEN `listServices` 使用未知但格式合法组件名 THEN 系统 SHALL 抛出 `IllegalArgumentException`（Unknown component）。
2. WHEN `listAllServices` 传入未知组件名 THEN 系统 SHALL 抛出 `IllegalArgumentException`（Unknown component）。
3. WHEN `listAllServices` 的 `componentName` 为空白 THEN 系统 SHALL 退化为全局服务视图。

### Requirement 2: 服务归属与组件解析分支补测

**用户故事:** 作为项目维护者，我希望服务归属判断和组件推断逻辑在边界场景下可验证，以防止调用入口与目录入口出现语义分叉。

#### 验收标准

1. WHEN `getServiceDetail` 传入空白服务名 THEN 系统 SHALL 抛出 `IllegalArgumentException`。
2. WHEN `serviceBelongsToComponent` 的 `serviceName` 为空 THEN 系统 SHALL 返回 `false`。
3. WHEN `resolveComponentName` 无显式命中时 THEN 系统 SHALL 按 service path 前缀进行 fallback 解析。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进都可复盘、可恢复。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

