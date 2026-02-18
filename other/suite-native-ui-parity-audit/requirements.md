---
name: suite-native-ui-parity-audit
category: other
description: Template for Suite Native Ui Parity Audit
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：三大应用原生 UI 行为对齐审计（64-06）

## 概述

在 `64-05` 能力总览基础上，补齐“原生 Moqui UI 行为对齐审计”能力：提供后端对齐审计接口，并在 Suite Hub 展示审计状态，确保“功能迁移完成”具备可核验证据。

## 需求 1：后端审计概览 API

### 验收标准

1.1 提供 `GET /api/v1/architecture/suite-hub/parity-audit`  
1.2 返回 HiveMind / PopCommerce / MarbleERP 分项审计数据  
1.3 返回总审计项、通过项、待补项、通过率与总体状态

## 需求 2：Suite Hub 审计可视化

### 验收标准

2.1 页面展示审计总览卡片（总项、通过、待补、通过率）  
2.2 页面展示总体状态标识（Aligned/In Progress）  
2.3 刷新动作可同步更新能力概览与审计概览

## 需求 3：测试与治理闭环

### 验收标准

3.1 后端 `ArchitectureGovernance` 相关测试通过  
3.2 前端构建通过  
3.3 scene/package/ontology/template 证据落盘并纳入 handoff

