---
name: wave6-api-capability-batchh
category: other
description: Template for Wave6 Api Capability Batchh
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch H（16-07）

## 概述

本 Spec 承接 `16-06`，继续补齐 routes 目录的“方法维度”可发现性。

目标：
- 提供 HTTP 方法目录入口
- 提供按方法浏览路由入口
- 在 `/api/v1` 与 OpenAPI 中显式暴露方法目录能力

## 功能需求

### Requirement 1: 方法目录入口

**用户故事:** 作为开发者，我希望知道每个 HTTP 方法下有多少路由，便于快速定位入口类型。

#### 验收标准

1. WHEN 调用 `GET /api/v1/methods` THEN 系统 SHALL 返回方法列表及每项 routeCount。
2. WHEN 返回方法项 THEN SHALL 包含 `method`、`routeCount`、`links.routes`。
3. WHEN 调用带过滤参数 THEN 系统 SHALL 支持 `component/publicOnly/tag/prefix` 过滤统计。

### Requirement 2: 按方法浏览路由

**用户故事:** 作为开发者，我希望直接查看某 HTTP 方法的路由集合。

#### 验收标准

1. WHEN 调用 `GET /api/v1/methods/{method}/routes` THEN 系统 SHALL 返回该方法路由集合。
2. WHEN `method` 大小写不同（如 get/GET） THEN 系统 SHALL 等价处理。
3. WHEN 无匹配 THEN 系统 SHALL 返回 200 + 空 routes。

### Requirement 3: 导航与文档同步

**用户故事:** 作为 API 使用者，我希望在导航与 docs 中看到方法目录入口。

#### 验收标准

1. WHEN 调用 `GET /api/v1` THEN SHALL 包含 `methods` 与 `methodRoutesPattern`。
2. WHEN 生成 OpenAPI THEN SHALL 包含 `/api/v1/methods` 与 `/api/v1/methods/{method}/routes`。
3. WHEN 生成 OpenAPI THEN 上述路径 SHALL 包含查询参数说明。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望 Batch H 可完整复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

