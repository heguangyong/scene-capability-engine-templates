---
name: suite-capability-observability
category: other
description: Template for Suite Capability Observability
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：三大应用能力对齐可观测（64-05）

## 概述

在 `64-04` 的 Suite Hub 入口基础上，补齐“能力对齐可观测”能力：提供后端统一能力概览 API，并在 Suite Hub 页面展示三大应用覆盖率与总览指标，支撑 Moqui 功能迁移闭环复核。

## 需求 1：后端能力概览 API

### 验收标准

1.1 提供 `GET /api/v1/architecture/suite-hub/capability-overview`  
1.2 返回三大应用（HiveMind/PopCommerce/MarbleERP）分项能力指标  
1.3 返回总览指标（total/full/partial/outOfScope/coverage）与治理状态

## 需求 2：Suite Hub 页面可观测增强

### 验收标准

2.1 页面展示能力总览卡片（总数、Full、Partial、覆盖率）  
2.2 每个应用卡片显示覆盖率标签  
2.3 失败场景展示错误提示，支持手动刷新

## 需求 3：测试与治理闭环

### 验收标准

3.1 后端 `ArchitectureGovernance` 服务/控制器测试通过  
3.2 前端构建通过  
3.3 `kse status --verbose` 与 `kse doctor --docs` 通过并纳入 handoff

