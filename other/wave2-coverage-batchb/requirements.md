---
name: wave2-coverage-batchb
category: other
description: Template for Wave2 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: Wave2 覆盖率实施 Batch B（12-05）

## 概述

本 Spec 承接 `12-04`，聚焦目录服务热点路径，优先覆盖 `ServiceCatalogService` 与 `EntityCatalogService` 的公开快照流程。

## 功能需求

### Requirement 1: ServiceCatalogService 快照路径补测

**用户故事:** 作为项目维护者，我希望目录服务在快照模式下的公开行为可验证，以降低 API 发现能力回归风险。

#### 验收标准

1. WHEN 使用预置快照调用 `listComponents/listServices/listAllServices` THEN 系统 SHALL 返回稳定结构与分页信息。
2. WHEN 调用 `getServiceDetail/serviceBelongsToComponent` THEN 系统 SHALL 覆盖命中、未命中与非法输入分支。

### Requirement 2: EntityCatalogService 快照路径补测

**用户故事:** 作为项目维护者，我希望实体目录在 component/package/keyword 筛选场景下行为可验证，以支持 API docs 发现能力稳定。

#### 验收标准

1. WHEN 使用预置快照调用 `listComponents/listEntities` THEN 系统 SHALL 覆盖排序、分页、过滤及 component 校验分支。
2. WHEN 输入非法 component 名称 THEN 系统 SHALL 返回一致的 `IllegalArgumentException` 语义。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望持续按 KSE 更新任务和上下文，保证跨会话可恢复推进。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

