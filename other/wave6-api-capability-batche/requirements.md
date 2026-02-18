---
name: wave6-api-capability-batche
category: other
description: Template for Wave6 Api Capability Batche
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch E（16-04）

## 概述

本 Spec 承接 `16-03`，继续强化“按组件组织 API”的可发现性与可导航性。

目标：
- 提供统一的组件导航中心入口
- 提供单组件聚合入口（services/entities/screens/routes/catalog 一站式）
- 保持现有路径兼容，新增能力以增量方式叠加

## 功能需求

### Requirement 1: 组件导航中心

**用户故事:** 作为开发者，我希望有一个专门的组件导航入口，快速了解所有组件相关 API 模式。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/hub` THEN 系统 SHALL 返回组件导航中心数据。
2. WHEN 返回导航中心 THEN 系统 SHALL 包含组件相关 pattern（overview/catalog/routes/services/entities/screens）。
3. WHEN 组件目录可用 THEN 系统 SHALL 返回组件矩阵摘要（至少包含 name、service/entity/screen 计数与链接）。

### Requirement 2: 单组件聚合入口

**用户故事:** 作为开发者，我希望访问单组件入口，一次看到该组件的主要 API 入口与统计。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/{componentName}` THEN 系统 SHALL 返回该组件聚合信息。
2. WHEN 返回聚合信息 THEN 系统 SHALL 提供 services/entities/screens/catalog/routes 的可调用链接。
3. WHEN 组件不存在 THEN 系统 SHALL 返回 `404 RESOURCE_NOT_FOUND`。

### Requirement 3: 目录链接一致性增强

**用户故事:** 作为开发者，我希望组件目录返回的链接字段完整且一致，避免在不同入口间来回猜测。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/catalog` THEN 每个组件 links SHALL 包含 `overview` 与 `routes`。
2. WHEN 调用 `GET /api/v1/components/{componentName}/catalog` THEN links SHALL 包含 `overview` 与 `routes`。
3. WHEN 调用 `GET /api/v1` THEN SHALL 暴露 `componentsHub` 与 `componentOverviewPattern` 导航字段。

### Requirement 4: OpenAPI 同步

**用户故事:** 作为 API 使用者，我希望在 Swagger/ReDoc 看到新增组件入口与参数说明。

#### 验收标准

1. WHEN 生成 OpenAPI THEN SHALL 包含 `/api/v1/components/hub` 与 `/api/v1/components/{componentName}`。
2. WHEN 生成 OpenAPI THEN `/api/v1/components/{componentName}` SHALL 包含 `includeSamples/sampleSize/includeGenericRoutes` 参数说明。
3. WHEN 生成 OpenAPI THEN SHALL 保持既有组件目录路径文档不回退。

### Requirement 5: KSE 闭环

**用户故事:** 作为项目维护者，我希望 Batch E 的推进可完整复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

