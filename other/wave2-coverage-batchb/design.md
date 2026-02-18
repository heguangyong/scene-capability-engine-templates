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

# Design: Wave2 覆盖率实施 Batch B（12-05）

## 设计目标

在不依赖真实 Moqui 运行时的前提下，通过反射注入快照对象覆盖目录服务的核心公开路径。

## 实施策略

1. **ServiceCatalogService**
   - 通过反射构造内部类：
     - `ServiceSummary`
     - `ComponentBucket`
     - `CatalogSnapshot`
   - 通过反射注入 `snapshot/snapshotAtMs`，命中 `getSnapshot()` 缓存分支。
   - 覆盖：
     - `listComponents`
     - `listServices`
     - `listAllServices`
     - `getServiceDetail`
     - `serviceBelongsToComponent`

2. **EntityCatalogService**
   - 通过反射构造内部类：
     - `EntitySummary`
     - `EntityCatalogSnapshot`
   - 注入 `snapshot/snapshotAtMs` 后覆盖：
     - `listComponents`（排序）
     - `listEntities`（component/packagePrefix/keyword/paging）
     - 非法 component 输入抛错

## 验证策略

1. 定向运行新增测试。
2. 运行 `mvn -q -pl app -am test`。
3. 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出 delta 报告。

