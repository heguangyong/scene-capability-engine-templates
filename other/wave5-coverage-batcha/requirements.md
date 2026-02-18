---
name: wave5-coverage-batcha
category: other
description: Template for Wave5 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave5 覆盖率实施 Batch A（15-00）

## 概述

本 Spec 承接 `14-02`，聚焦 `EntityCatalogService#listRelationships` 与 `EntityCrudService#batch/listRelated` 的深层分支，目标是增强实体 API 复杂工作流的回归稳定性。

## 功能需求

### Requirement 1: 实体关系目录分支补测

**用户故事:** 作为项目维护者，我希望实体关系目录在定义缺失与关系字段缺省场景下行为稳定，以避免关系浏览能力回归。

#### 验收标准

1. WHEN `listRelationships` 依赖定义不存在 THEN 系统 SHALL 返回 `null`。
2. WHEN 定义存在但缺少 `relationships` 字段 THEN 系统 SHALL 返回空数组。
3. WHEN 定义存在且包含关系元数据 THEN 系统 SHALL 返回稳定的 `entityName/relationshipCount/relationships` 结构。

### Requirement 2: EntityCrud 批处理与关联查询分支补测

**用户故事:** 作为项目维护者，我希望批处理和关联查询在边界输入下保持明确错误语义，以减少线上批操作失败的诊断成本。

#### 验收标准

1. WHEN `batch.operations` 非数组或超限 THEN 系统 SHALL 返回 `400 VALIDATION_ERROR`。
2. WHEN `batch` 使用 `action` 作为别名且 `entityId` 字段存在 THEN 系统 SHALL 正确执行对应操作。
3. WHEN `listRelated` 的 fields/orderBy/filter 非法 THEN 系统 SHALL 在访问运行时前抛出 `IllegalArgumentException`。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进均有完整可追踪证据。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

