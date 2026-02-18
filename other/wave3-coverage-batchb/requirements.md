---
name: wave3-coverage-batchb
category: other
description: Template for Wave3 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave3 覆盖率实施 Batch B（13-01）

## 概述

本 Spec 承接 `13-00`，聚焦 `EntityCrudService` 非 batch 主路径的输入校验与前置分支，优先覆盖运行时依赖之前的行为边界。

## 功能需求

### Requirement 1: list/listRelated 前置校验分支补测

**用户故事:** 作为项目维护者，我希望实体查询 API 在进入运行时前的输入校验行为可验证，以避免非法参数绕过导致不可控异常。

#### 验收标准

1. WHEN `list` 的 `fields/orderBy/filter` 非法 THEN 系统 SHALL 抛出一致的 `IllegalArgumentException`，且不触发执行上下文调用。
2. WHEN `listRelated` 的 `relationshipName` 非法 THEN 系统 SHALL 抛出一致的 `IllegalArgumentException`，且不触发执行上下文调用。

### Requirement 2: create/update/delete 实体名前置校验补测

**用户故事:** 作为项目维护者，我希望 CRUD 操作在非法实体名场景下立即失败，以降低路径穿越和格式异常风险。

#### 验收标准

1. WHEN `create/update/delete` 传入非法 entityName THEN 系统 SHALL 在访问执行上下文前抛出校验错误。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进都可追踪可恢复。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

