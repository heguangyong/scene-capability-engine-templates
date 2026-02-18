---
name: wave1-coverage-batchb
category: other
description: Template for Wave1 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave1 覆盖率实施 Batch B（12-02）

## 概述

本 Spec 承接 `12-01`，聚焦 Wave1 Batch B：通过低风险单元/属性测试覆盖控制器与批处理内部逻辑，提升 app 层热点类有效覆盖。

## 功能需求

### Requirement 1: Batch B 热点类补测

**用户故事:** 作为项目维护者，我希望补齐 Batch B 指定类测试，以便持续推进覆盖率提升。

#### 验收标准

1. WHEN 执行 Batch B THEN 系统 SHALL 为 `EntityCrudService` 补充批处理内部逻辑测试。
2. WHEN 执行 Batch B THEN 系统 SHALL 为 `EntityController` 与 `ServiceController` 补充参数解析契约测试。
3. WHEN 新增测试完成 THEN 系统 SHALL 保持现有测试链路通过。

### Requirement 2: 回采与增量可比对

**用户故事:** 作为项目维护者，我希望每一批都有量化结果，以便复盘与策略迭代。

#### 验收标准

1. WHEN Batch B 完成 THEN 系统 SHALL 回采质量基线报告。
2. WHEN 基线完成 THEN 系统 SHALL 给出与上一批（12-01）的 instruction/branch 对比。

### Requirement 3: KSE 持续闭环

**用户故事:** 作为项目维护者，我希望跨会话无断点推进，避免上下文丢失。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

