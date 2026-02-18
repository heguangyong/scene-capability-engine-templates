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

# Design: Wave1 覆盖率实施 Batch B（12-02）

## 设计目标

继续采用低耦合、低风险策略，优先覆盖静态/私有纯逻辑与参数解析契约，避免引入重型集成依赖。

## 实施策略

1. **EntityCrudService**
   - 通过反射覆盖批处理辅助逻辑：
     - `normalizeOperationName`
     - `firstNonBlank`
     - `extractErrorMessage`
     - `buildBatchSuccess`
     - `buildBatchFailure`
     - `buildBatchAbortResponse`

2. **EntityController**
   - 通过反射覆盖参数解析：
     - `parseIntParam`
     - `parseOptionalInt`
     - `parseBoolean`

3. **ServiceController**
   - 通过反射覆盖参数解析：
     - `parseIntOrNull`
     - `parseBoolean`

## 验证策略

1. 定向运行新增测试。
2. 运行 `mvn -q -pl app -am test`。
3. 调用 `scripts/quality-baseline.ps1`（SpecSlug 指向 12-02）回采报告。

