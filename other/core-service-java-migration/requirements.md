---
name: core-service-java-migration
category: other
description: Template for Core Service Java Migration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements: 03-02 {{SPEC_NAME_TITLE}}

## 1. 概述

本 Spec 聚焦将核心 Groovy 服务（约 20%-30%）迁移为 Java，实现可验证的“首批闭环”。

- Spec ID: `{{SPEC_NAME}}`
- 来源主计划: `03-00-moqui-refactoring-master-plan`
- 主线: A（去 Groovy 化）
- 依赖: `03-01-groovy-usage-deep-analysis`
- 预估周期: 4-6 周

## 2. 目标

1. 建立 Java 服务迁移模板与编码规范。
2. 完成首批核心服务迁移并通过编译与关键测试。
3. 输出性能对比与迁移经验，供批量迁移复用。

## 3. 功能需求

### 3.1 核心服务迁移实施

系统必须完成优先级最高的一批 Groovy 服务 Java 化。

验收标准:
- WHEN 首批迁移完成 THEN 迁移范围覆盖 `03-01` 推荐队列中的核心服务。
- WHEN 提交结果 THEN 每个服务具备等价接口与可回归行为。

### 3.2 Java 模板与规范固化

系统必须沉淀统一的 Java 服务实现模板。

验收标准:
- WHEN 模板发布 THEN 包含异常处理、日志、事务边界约定。
- WHEN 新服务落地 THEN 能按模板快速复用。

### 3.3 测试与性能基线

系统必须补齐首批迁移服务的验证基线。

验收标准:
- WHEN 首批迁移完成 THEN 关键路径单测/集成测试通过。
- WHEN 性能对比完成 THEN 与迁移前差异不超过 10%（关键场景）。

## 4. 非功能需求

1. 类型安全: 避免动态反射替代导致的隐性风险。
2. 可调试性: 迁移后支持标准 Java 断点与调用栈分析。
3. 可回滚性: 每批迁移具备独立回退策略。

## 5. 交付物

1. Java 迁移模板与编码规范文档。
2. 首批核心服务 Java 实现清单。
3. 迁移测试报告与性能对比报告。
4. 批量迁移复用指南（输入 `03-03`）。
