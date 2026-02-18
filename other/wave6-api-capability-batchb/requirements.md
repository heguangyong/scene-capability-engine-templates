---
name: wave6-api-capability-batchb
category: other
description: Template for Wave6 Api Capability Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave6 API 能力增强 Batch B（16-01）

## 概述

本 Spec 承接 `16-00`，从“覆盖率批次”切换到“API 能力线”，目标是把 Moqui 后端 API 的发现入口、目录聚合与路由检索能力做成更接近 FastAPI 风格的开发体验：入口清晰、分组明确、文档可发现。

## 功能需求

### Requirement 1: `/api/v1` 发现入口增强

**用户故事:** 作为 API 使用者，我希望打开一个入口就能看到 docs、健康检查、目录与分组入口，减少上手成本。

#### 验收标准

1. WHEN 调用 `GET /api/v1` THEN 系统 SHALL 返回兼容旧字段的同时提供分组化导航（docs/monitoring/catalog/hubs/components）。
2. WHEN 调用 `GET /api/v1` THEN 系统 SHALL 提供 quickstart 信息（`/docs`、`/openapi.json`、`/health` 等）。
3. WHEN 现有客户端仍依赖旧平铺字段 THEN 系统 SHALL 保持旧字段不移除。

### Requirement 2: `/api/v1/catalog` 聚合能力增强

**用户故事:** 作为 API 平台维护者，我希望一次请求即可看到 services/entities/screens 的聚合统计与组件分布，便于平台总览。

#### 验收标准

1. WHEN 调用 `GET /api/v1/catalog` THEN 系统 SHALL 返回各目录总量摘要（services/entities/screens/components）。
2. WHEN 调用 `GET /api/v1/catalog` THEN 系统 SHALL 返回按组件聚合的矩阵视图（每个组件的 service/entity/screen 数量）。
3. WHEN 目录服务未初始化 THEN 系统 SHALL 返回 `SERVICE_UNAVAILABLE`（503）。

### Requirement 3: `/api/v1/routes` 可检索能力增强

**用户故事:** 作为 API 集成开发者，我希望按前缀、方法、公开性快速过滤路由，提升定位效率。

#### 验收标准

1. WHEN 调用 `GET /api/v1/routes?prefix=/api/v1/services` THEN 系统 SHALL 仅返回匹配该前缀的路由。
2. WHEN 调用 `GET /api/v1/routes?method=post` THEN 系统 SHALL 仅返回该 HTTP 方法路由（大小写不敏感）。
3. WHEN 同时传入 `tag/publicOnly/prefix/method` THEN 系统 SHALL 组合过滤并返回稳定统计字段（如 totalCount/tags/methods）。

### Requirement 4: OpenAPI 文档可发现性同步

**用户故事:** 作为 API 使用者，我希望 Swagger/ReDoc 中可直接看到新增过滤参数与一致标签，避免“接口有能力但文档无提示”。

#### 验收标准

1. WHEN 生成 OpenAPI THEN 系统 SHALL 为 `/api/v1/routes` 补充 `prefix`、`method` 查询参数说明。
2. WHEN 路由进行标签推断 THEN 系统 SHALL 在目录接口与 OpenAPI 使用一致的标签规则。
3. WHEN 访问 `/docs` 和 `/redoc` THEN 新增能力 SHALL 在文档中可见。

### Requirement 5: KSE 闭环

**用户故事:** 作为项目维护者，我希望本批次具备完整可追踪证据，避免会话中断造成上下文断裂。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 及时更新 `tasks.md` 状态。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`（本轮推进、验证命令、下一步）。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status`、`kse docs validate --all`、`scripts/kse-continuity-check.ps1`。

