---
name: wave4-coverage-batchc
category: other
description: Template for Wave4 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave4 覆盖率实施 Batch C（14-02）

## 概述

本 Spec 承接 `14-01`，聚焦 `EntityCatalogService` 与 `EntityCrudService` 的参数解析和目录查询边界分支，目标是提高实体 API 基础能力的回归稳定性。

## 功能需求

### Requirement 1: EntityCatalogService 边界分支补测

**用户故事:** 作为项目维护者，我希望实体目录查询在未知组件、分页边界和组件推断场景下返回稳定结构，避免前端开发工具链出现不可预测行为。

#### 验收标准

1. WHEN `listEntities` 传入未知但格式合法组件名 THEN 系统 SHALL 返回空列表且结构稳定。
2. WHEN `listEntities` 的分页参数越界 THEN 系统 SHALL 使用归一化后分页信息返回。
3. WHEN `inferComponentFromPackage` 输入不同命名空间 THEN 系统 SHALL 按规则返回 `framework` / `mantle-udm` / `null`。

### Requirement 2: EntityCrudService 解析分支补测

**用户故事:** 作为项目维护者，我希望实体 CRUD 的排序/字段/筛选解析逻辑在边界输入时有明确行为，避免请求参数回归破坏业务接口。

#### 验收标准

1. WHEN `parseOrderByList` 输入 `+field`、`-field`、空白 token THEN 系统 SHALL 输出规范化结果或抛出明确异常。
2. WHEN `sanitizeFilterParams` 输入 `null`、空值、保留参数 THEN 系统 SHALL 返回预期过滤结果。
3. WHEN `parseFieldList` 输入空白或多字段 THEN 系统 SHALL 保持稳定输出并拒绝非法字段。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进均可追踪、可恢复。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

