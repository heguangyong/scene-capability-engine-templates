---
name: wave6-api-capability-batchf
category: other
description: Template for Wave6 Api Capability Batchf
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch F（16-05）

## 概述

本 Spec 承接 `16-04`，在“组件可发现性”基础上继续提升 API 可检索能力。

目标：
- 提供统一的 API 搜索入口（服务/实体/屏幕/路由）
- 提供组件内搜索入口，避免用户手动拼接组件过滤
- 在 docs 与 `/api/v1` 导航中直观暴露搜索能力

## 功能需求

### Requirement 1: 全局 API 搜索入口

**用户故事:** 作为开发者，我希望通过一个关键词快速搜索全量 API 资产。

#### 验收标准

1. WHEN 调用 `GET /api/v1/search?q=login` THEN 系统 SHALL 返回 services/entities/screens/routes 四类匹配结果。
2. WHEN 指定 `component` THEN 系统 SHALL 将搜索约束到该组件（路由、目录均受限）。
3. WHEN 返回结果 THEN 系统 SHALL 包含每类 `totalCount` 与聚合 `summary.totalMatches`。

### Requirement 2: 组件内搜索入口

**用户故事:** 作为开发者，我希望直接在组件入口执行搜索，不用重复传 component 参数。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/{componentName}/search?q=user` THEN 系统 SHALL 返回该组件的搜索结果。
2. WHEN 组件不存在 THEN 系统 SHALL 返回 `404 RESOURCE_NOT_FOUND`。
3. WHEN 未传 `q` THEN 系统 SHALL 返回 `400 VALIDATION_ERROR`。

### Requirement 3: 导航与文档可发现性

**用户故事:** 作为 API 使用者，我希望在导航和 OpenAPI 中直接看到搜索入口。

#### 验收标准

1. WHEN 调用 `GET /api/v1` THEN SHALL 包含 `search` 与 `componentSearchPattern` 导航字段。
2. WHEN 生成 OpenAPI THEN SHALL 包含 `/api/v1/search` 与 `/api/v1/components/{componentName}/search`。
3. WHEN 生成 OpenAPI THEN 上述路径 SHALL 包含 `q/component/size`（或组件路径等价参数）说明。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望 Batch F 可完整复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

