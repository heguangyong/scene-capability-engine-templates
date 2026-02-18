---
name: suite-hub-ui-integration
category: other
description: Template for Suite Hub Ui Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：三大应用入口页集成（64-04）

## 1. 设计目标

- 保留现有“领域导航”不变；
- 增加“应用语义导航”作为补充入口；
- 让三大应用映射从文档落到可交互 UI。

## 2. 集成点

- 导航配置：`frontend/src/renderer/config/menu-config.js`
- 路由配置：`frontend/src/renderer/router/index.js`
- 导航状态：`frontend/src/renderer/stores/navigation.ts`
- 新视图：`frontend/src/renderer/views/SuiteHubView.vue`

## 3. 页面结构

1. 三大应用卡片（HiveMind/PopCommerce/MarbleERP）  
2. 每个应用卡片包含模块快捷入口  
3. 追加跨套件关键入口（审批中心、报表运维、迁移验证）

## 4. 完成定义

1. 路由可达且导航可高亮  
2. 模块跳转链路可用  
3. 构建与治理检查通过



## Ontology Model (Backfilled)

### Entities
- **SuiteHubUiIntegrationRecord**: Core domain record for Suite Hub Ui Integration scenarios.
- **SuiteHubUiIntegrationProcess**: Process context handling lifecycle transitions.
- **SuiteHubUiIntegrationAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **SuiteHubUiIntegrationRecord** 1:N **SuiteHubUiIntegrationProcess** (lifecycle orchestration).
- **SuiteHubUiIntegrationProcess** 1:N **SuiteHubUiIntegrationAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
