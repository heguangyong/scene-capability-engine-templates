---
name: wave6-api-capability-batchc
category: other
description: Template for Wave6 Api Capability Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch C（16-02）

## 概述

本 Spec 承接 `16-01`，继续沿“API 能力线”推进，聚焦组件视角的可发现性：在服务/实体/屏幕分组入口之上，提供统一的组件目录与单组件 API 目录详情，提升 Moqui 全量 API 的组织清晰度与文档可复盘性。

## 功能需求

### Requirement 1: 组件目录总览 API

**用户故事:** 作为 API 平台维护者，我希望一眼看到每个组件的 service/entity/screen 规模与入口链接，便于快速导航。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/catalog` THEN 系统 SHALL 返回组件矩阵（serviceCount/entityCount/screenCount/totalApiItems）。
2. WHEN 调用 `GET /api/v1/components/catalog` THEN 系统 SHALL 返回每个组件的链接集合（services/entities/screens/catalog）。
3. WHEN 目录服务未初始化 THEN 系统 SHALL 返回 `SERVICE_UNAVAILABLE`（503）。

### Requirement 2: 单组件 API 目录详情

**用户故事:** 作为 API 使用者，我希望针对某个组件直接查看该组件的 API 目录摘要和示例条目，而不是在多个接口间手工拼装。

#### 验收标准

1. WHEN 调用 `GET /api/v1/components/{componentName}/catalog` THEN 系统 SHALL 返回该组件 counts + links。
2. WHEN 指定 `includeSamples=true` THEN 系统 SHALL 返回 services/entities/screens 的示例条目。
3. WHEN 组件不存在 THEN 系统 SHALL 返回 `RESOURCE_NOT_FOUND`（404）。

### Requirement 3: OpenAPI 文档与入口同步

**用户故事:** 作为 API 使用者，我希望在 `/docs` 中能直接发现新增组件目录端点与可选参数。

#### 验收标准

1. WHEN 生成 OpenAPI THEN 系统 SHALL 包含新增路径：`/api/v1/components/catalog` 与 `/api/v1/components/{componentName}/catalog`。
2. WHEN 查看 `/api/v1/components/{componentName}/catalog` 文档 THEN 系统 SHALL 暴露 `includeSamples` 与 `sampleSize` 查询参数说明。
3. WHEN 调用 `GET /api/v1` THEN 系统 SHALL 提供到新增组件目录端点的导航链接。

### Requirement 4: KSE 闭环

**用户故事:** 作为项目维护者，我希望本批次继续保持会话可恢复与成果可复盘。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

