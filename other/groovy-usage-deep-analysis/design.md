---
name: groovy-usage-deep-analysis
category: other
description: Template for Groovy Usage Deep Analysis
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: 03-01 {{SPEC_NAME_TITLE}}

## 1. 设计目标

以“可执行调研”方式将历史经验沉淀为结构化基线，直接服务后续迁移阶段。

## 2. 总体流程

1. 文件扫描: 统计 `.groovy`、XML actions/script 片段。
2. 依赖分析: 建立服务调用关系与模块影响面。
3. 风险分级: 基于复杂度、耦合度、业务关键度打分。
4. 优先级输出: 形成 P0/P1/P2 迁移队列。

## 3. 数据产物设计

### 3.1 清单数据

字段建议:
- `path`
- `module`
- `runtime_scope` (`prod`/`test`)
- `type` (`service`/`xml-action`/`other`)
- `complexity_level`
- `risk_level`

### 3.2 风险矩阵

维度:
- 技术复杂度（低/中/高）
- 业务关键度（低/中/高）
- 替代可行性（高/中/低）

## 4. 与主计划追溯

- 对应 `03-00` 需求: 1.1, 1.2
- 输出供给: `03-02-core-service-java-migration`

## 5. 验证策略

1. 抽样核对 20+ 条清单记录，确认分类准确。
2. 对高风险项进行人工复核，确保调用链可解释。
3. 用同一脚本重复执行，确认结果稳定。
