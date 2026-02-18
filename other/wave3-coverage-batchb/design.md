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

# Design: Wave3 覆盖率实施 Batch B（13-01）

## 设计目标

通过轻量代理桩与异常断言，覆盖 `EntityCrudService` 非 batch 主路径中“运行时前”的关键输入分支，提升回归防护强度。

## 实施策略

1. **新增 `EntityCrudServiceWorkflowPropertyTest`**
   - 使用最小 `ExecutionContextFactory` 代理桩，记录 `getExecutionContext` 调用次数。
   - 对于应在前置校验失败的场景，断言调用次数为 0。

2. **覆盖分支**
   - `list`：非法 `fields` / `orderBy` / `filter key`。
   - `listRelated`：非法 `relationshipName`。
   - `create/update/delete`：非法 `entityName`。

3. **验证策略**
   - 定向运行新增测试类。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `13-00` 的增量报告。

