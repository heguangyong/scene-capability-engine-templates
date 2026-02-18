---
name: wave3-coverage-batcha
category: other
description: Template for Wave3 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave3 覆盖率实施 Batch A（13-00）

## 设计目标

以低成本、低耦合方式覆盖 `OpenApiGenerator` 的深层转换分支，优先增强结构稳定性测试而非运行时集成。

## 实施策略

1. **扩展现有 `OpenApiGeneratorPropertyTest`**
   - 追加针对 `convertPaths`/`convertResponses` 的边界样例。
   - 复用现有反射入口，避免改动生产代码。

2. **重点覆盖分支**
   - 仅 body 参数场景（确保 `parameters` 被移除）。
   - response 缺省描述场景（description 默认值）。
   - definitions -> components.schemas 的透传完整性。
   - 默认 entity detail 路径的 `get/put/delete` 响应码契约。

## 验证策略

1. 定向运行 `OpenApiGeneratorPropertyTest`。
2. 运行 `mvn -q -pl app -am test`。
3. 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `12-06` 的增量报告。

