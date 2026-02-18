---
name: wave3-coverage-batcha
category: other
description: Template for Wave3 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave3 覆盖率实施 Batch A（13-00）

## 概述

本 Spec 承接 `12-06`，进入 Wave3 的第一批覆盖率回采，优先聚焦 `OpenApiGenerator` 的深层转换分支与默认路径契约。

## 功能需求

### Requirement 1: OpenAPI 转换分支补测

**用户故事:** 作为项目维护者，我希望 Swagger2 -> OpenAPI3 转换逻辑的边界分支可验证，以避免文档结构回归。

#### 验收标准

1. WHEN 输入包含 `definitions` THEN 系统 SHALL 将其映射到 `components.schemas`。
2. WHEN 操作参数仅包含 `body` THEN 系统 SHALL 生成 `requestBody` 且移除 `parameters`。
3. WHEN 响应未提供 `description/schema` THEN 系统 SHALL 生成兼容的最小响应结构。

### Requirement 2: 默认路径契约补测

**用户故事:** 作为项目维护者，我希望默认路径（auth/entities/docs）的安全与响应契约可验证，以保证 API docs 可用性。

#### 验收标准

1. WHEN 生成默认路径 THEN 系统 SHALL 区分公开端点与受保护端点的 `security` 语义。
2. WHEN 生成实体路径 THEN 系统 SHALL 保持参数与响应码结构稳定（GET/PUT/DELETE）。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每一轮覆盖率推进都可追踪可恢复。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

