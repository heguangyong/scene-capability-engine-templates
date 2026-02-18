---
name: wave5-coverage-batchb
category: other
description: Template for Wave5 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave5 覆盖率实施 Batch B（15-01）

## 概述

本 Spec 承接 `15-00`，聚焦 `EntityCrudService` 的复合主键辅助方法与 `EntityCatalogService` 的静态推断/筛选边界分支，目标是加固实体层复杂参数路径。

## 功能需求

### Requirement 1: 复合主键辅助方法分支补测

**用户故事:** 作为项目维护者，我希望实体复合主键条件拼装行为可回归验证，以避免复合主键实体查询/更新出现隐性回归。

#### 验收标准

1. WHEN `setPkCondition` 针对单主键/复合主键 THEN 系统 SHALL 按规则写入条件。
2. WHEN `addPkToParams` 输入复合主键值数量不足 THEN 系统 SHALL 仅写入可映射部分。
3. WHEN 主键值含空格 THEN 系统 SHALL 执行 trim 后写入。

### Requirement 2: 实体目录静态推断边界补测

**用户故事:** 作为项目维护者，我希望实体目录静态工具在边界输入下行为稳定，以降低目录能力回归风险。

#### 验收标准

1. WHEN `filter` 使用 keyword 但 packagePrefix 为空 THEN 系统 SHALL 正确按关键字过滤。
2. WHEN `packageNameOf` 输入末尾点号或无包名 THEN 系统 SHALL 返回稳定结果。
3. WHEN `normalizeComponentName` 输入含前后空格 THEN 系统 SHALL trim 后返回。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进都具备完整可追踪证据。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

