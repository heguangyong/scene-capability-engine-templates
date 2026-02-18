---
name: wave3-coverage-batchc
category: other
description: Template for Wave3 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave3 覆盖率实施 Batch C（13-02）

## 设计目标

以纯单元测试方式覆盖 `ApiMetricsService` 聚合、排序、状态分桶与导出文本分支，确保监控契约稳定。

## 实施策略

1. **新增 `ApiMetricsServicePropertyTest`**
   - 直接实例化 `ApiMetricsService`。
   - 通过多组 `beginRequest/finishRequest` 组合驱动关键分支。

2. **覆盖重点**
   - inflight 下界保护（不为负）。
   - duration 归一化（负值变 0）。
   - status class 分桶（unknown/other/2xx/4xx/5xx）。
   - `snapshot().endpoints` 排序规则与字段完整性。
   - `prometheusText()` label 转义。

3. **验证策略**
   - 定向运行新增测试类。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `13-01` 增量报告。

