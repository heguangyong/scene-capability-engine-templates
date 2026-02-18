---
name: wave6-api-capability-batchg
category: other
description: Template for Wave6 Api Capability Batchg
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch G（16-06）

## 概述

本 Spec 承接 `16-05`，继续提升 API 目录可发现性：新增“标签维度”导航与路由目录。

目标：
- 提供全局标签目录入口
- 提供按标签浏览路由入口
- 在 `/api/v1` 与 OpenAPI 中显式暴露标签能力

## 功能需求

### Requirement 1: 标签目录入口

**用户故事:** 作为开发者，我希望快速看到系统有哪些 API 标签及对应规模。

#### 验收标准

1. WHEN 调用 `GET /api/v1/tags` THEN 系统 SHALL 返回标签列表与每个标签的 routeCount。
2. WHEN 返回标签列表 THEN 每项 SHALL 包含 `tag`、`routeCount`、`links.routes`。
3. WHEN 调用带过滤参数 THEN 系统 SHALL 支持 `component/publicOnly/method/prefix` 过滤统计。

### Requirement 2: 按标签浏览路由

**用户故事:** 作为开发者，我希望直接查看某个标签下的路由集合。

#### 验收标准

1. WHEN 调用 `GET /api/v1/tags/{tag}/routes` THEN 系统 SHALL 返回该标签路由集合。
2. WHEN 组合过滤参数 THEN 系统 SHALL 支持 `component/publicOnly/method/prefix` 组合过滤。
3. WHEN 标签无匹配 THEN 系统 SHALL 返回 200 + 空 routes（非错误）。

### Requirement 3: 导航与文档同步

**用户故事:** 作为 API 使用者，我希望在导航与 docs 中看到标签目录入口。

#### 验收标准

1. WHEN 调用 `GET /api/v1` THEN SHALL 包含 `tags` 与 `tagRoutesPattern`。
2. WHEN 生成 OpenAPI THEN SHALL 包含 `/api/v1/tags` 与 `/api/v1/tags/{tag}/routes`。
3. WHEN 生成 OpenAPI THEN 上述路径 SHALL 包含必要查询参数说明。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望 Batch G 可完整复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

