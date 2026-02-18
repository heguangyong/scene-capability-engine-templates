---
name: wave4-coverage-batchb
category: other
description: Template for Wave4 Coverage Batchb
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave4 覆盖率实施 Batch B（14-01）

## 设计目标

以注入快照 + 反射调用的轻量测试方式，补齐 `ServiceCatalogService` 在查询参数、组件归属和组件解析路径上的关键分支。

## 实施策略

1. **扩展 `ServiceCatalogServiceSnapshotWorkflowPropertyTest`**
   - 继续复用 `injectSnapshot()` 技术，避免依赖完整 Moqui runtime。
   - 增加未知组件、空白组件名、空白服务名等异常与退化分支。

2. **扩展 `ServiceCatalogServiceInternalsPropertyTest`**
   - 通过反射测试 `resolveComponentName()` fallback 逻辑。
   - 验证路径前缀命中与完全未命中场景。

3. **覆盖重点**
   - `listServices` / `listAllServices` 的 Unknown component 分支。
   - `getServiceDetail` 参数前置校验。
   - `serviceBelongsToComponent` 的 null 快速返回分支。
   - `resolveComponentName` 的显式命中与 path fallback 命中。

4. **验证策略**
   - 定向运行 `ServiceCatalogService*` 相关测试。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `14-00` 的增量报告。

