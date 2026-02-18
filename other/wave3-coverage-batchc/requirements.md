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

# Requirements: Wave3 覆盖率实施 Batch C（13-02）

## 概述

本 Spec 承接 `13-01`，聚焦 `ApiMetricsService` 的核心统计与导出分支，目标是提升监控能力的回归可验证性。

## 功能需求

### Requirement 1: 指标聚合分支补测

**用户故事:** 作为项目维护者，我希望请求统计在不同状态码、方法、耗时场景下聚合正确，以保证监控数据可信。

#### 验收标准

1. WHEN `finishRequest` 输入负耗时 THEN 系统 SHALL 将耗时归一为非负。
2. WHEN `finishRequest` 在无 inflight 基础上被调用 THEN 系统 SHALL 保持 inflight 不小于 0。
3. WHEN `method/status` 为边界值 THEN 系统 SHALL 落入正确分桶（UNKNOWN/unknown/other/2xx...）。

### Requirement 2: 快照与 Prometheus 导出补测

**用户故事:** 作为项目维护者，我希望 `snapshot/prometheusText` 输出结构稳定，以避免观测面回归。

#### 验收标准

1. WHEN 生成 snapshot THEN 系统 SHALL 返回 totals/byMethod/byStatusClass/endpoints 结构。
2. WHEN endpoint 含特殊字符 THEN 系统 SHALL 在 Prometheus 文本中进行 label 转义。
3. WHEN 多端点请求数不同 THEN snapshot 端点列表 SHALL 按请求数降序排序。

### Requirement 3: KSE 闭环

**用户故事:** 作为项目维护者，我希望每批次推进都可追踪可恢复。

#### 验收标准

1. WHEN 子任务完成 THEN 系统 SHALL 更新 `tasks.md`。
2. WHEN 会话收尾 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。
3. WHEN 收尾校验 THEN 系统 SHALL 执行 `kse status` 与 `kse docs validate --all`。

