---
name: wave6-api-capability-batchd
category: other
description: Template for Wave6 Api Capability Batchd
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch D（16-03）

## 概述

本 Spec 承接 `16-02`，继续强化“全量 API 可发现性”。重点补齐组件视角的路由目录能力：
- 在全局 `/api/v1/routes` 上支持 `component` 过滤
- 提供组件专属路由目录入口 `/api/v1/components/{componentName}/routes`
- 在 docs 中可见上述能力，确保调用体验与 FastAPI 风格一致（入口直观、参数明确）

## 功能需求

### Requirement 1: 全局路由增加组件过滤

**用户故事:** 作为开发者，我希望在全局路由列表中按组件过滤，快速定位组件 API。

#### 验收标准

1. WHEN 调用 `GET /api/v1/routes?component=framework` THEN 系统 SHALL 仅返回 framework 相关路由。
2. WHEN `component` 与 `tag/method/publicOnly/prefix` 同时存在 THEN 系统 SHALL 执行组合过滤。
3. WHEN 返回路由列表 THEN 每条路由 SHALL 包含 `component` 归属字段（可为 `global`/`{componentName}`/具体组件）。

### Requirement 2: 单组件路由目录入口

**用户故事:** 作为开发者，我希望直接进入某组件的路由目录，而不是手动拼接全局过滤参数。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/{componentName}/routes` THEN 系统 SHALL 返回该组件路由集合。
2. WHEN `includeGeneric=true` THEN 系统 SHALL 包含通用组件路由模板（`/api/v1/components/{componentName}/...`）。
3. WHEN 组件无匹配路由 THEN 系统 SHALL 返回空 routes（200）而非报错。

### Requirement 3: OpenAPI 同步

**用户故事:** 作为 API 使用者，我希望在 Swagger/ReDoc 中看到组件过滤与组件路由目录参数说明。

#### 验收标准

1. WHEN 生成 OpenAPI THEN `/api/v1/routes` SHALL 包含 `component` 参数说明。
2. WHEN 生成 OpenAPI THEN `/api/v1/components/{componentName}/routes` SHALL 包含 `includeGeneric` 及常见过滤参数。
3. WHEN 访问 `/api/v1` THEN SHALL 提供新增组件路由目录导航字段。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望该批次仍保持严格可复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

