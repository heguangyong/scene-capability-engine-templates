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

# Requirements: 03-01 {{SPEC_NAME_TITLE}}

## 1. 概述

本 Spec 用于对 Moqui 系统中的 Groovy 使用现状进行可执行调研，形成后续 Java 迁移的基线数据与优先级。

- Spec ID: `{{SPEC_NAME}}`
- 来源主计划: `03-00-moqui-refactoring-master-plan`
- 主线: A（去 Groovy 化）
- 依赖: 无
- 预估周期: 2-3 周

## 2. 目标

1. 建立生产环境 Groovy 资产清单（文件、用途、模块、调用关系）。
2. 识别 XML 内嵌逻辑与动态执行热点。
3. 输出可执行迁移优先级和风险分级，为 `03-02` 提供输入。

## 3. 功能需求

### 3.1 Groovy 资产盘点

系统必须对 `backend` 范围内 Groovy 相关文件完成全量扫描并分类。

验收标准:
- WHEN 扫描完成 THEN 输出生产/测试分类清单。
- WHEN 清单生成 THEN 每条记录包含路径、模块、用途、复杂度标签。

### 3.2 XML Actions 逻辑识别

系统必须识别 XML 中 `actions/script` 逻辑片段，并建立与服务能力的映射。

验收标准:
- WHEN 扫描 XML THEN 输出包含逻辑标签的文件列表。
- WHEN 映射完成 THEN 给出“可直接迁移 / 需重构后迁移”判断。

### 3.3 依赖与调用链分析

系统必须给出高风险 Groovy 服务的调用链与依赖影响面。

验收标准:
- WHEN 分析完成 THEN 输出 TOP 风险服务列表（至少 20 项或全部高风险项）。
- WHEN 报告生成 THEN 每项包含上游调用者与下游依赖。

### 3.4 迁移优先级建议

系统必须形成用于执行阶段的优先级队列。

验收标准:
- WHEN 优先级生成 THEN 至少包含 P0/P1/P2 三层。
- WHEN 输出结论 THEN 明确 `03-02` 首批候选服务（20%-30%）。

## 4. 非功能需求

1. 可追溯性: 所有结论需可回溯到文件级证据。
2. 可复用性: 产物采用 Markdown + 结构化清单（CSV/JSON）双格式。
3. 可维护性: 扫描脚本应支持重复执行与增量复扫。

## 5. 交付物

1. Groovy 使用清单（生产/测试/模块维度）。
2. XML 逻辑识别报告。
3. 风险矩阵与优先级队列。
4. `03-02` 首批迁移候选列表。
