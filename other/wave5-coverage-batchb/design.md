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

# Design: Wave5 覆盖率实施 Batch B（15-01）

## 设计目标

通过反射 + 代理桩的轻量方式，补齐 `EntityCrudService` 主键辅助方法与 `EntityCatalogService` 静态筛选分支，避免重型运行时依赖。

## 实施策略

1. **新增 `EntityCrudServicePkHelperPropertyTest`**
   - 通过动态代理模拟 `EntityFind`、`EntityDefinition`。
   - 反射调用私有方法 `setPkCondition`、`addPkToParams`。

2. **扩展 `EntityCatalogServiceInternalsPropertyTest`**
   - 增加 `filter` 在仅 keyword 情况下的过滤验证。
   - 增加 `packageNameOf` 边界输入用例。

3. **验证策略**
   - 定向运行 `EntityCrudServicePkHelperPropertyTest` 与 `EntityCatalogServiceInternalsPropertyTest`。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `15-00` 增量报告。

