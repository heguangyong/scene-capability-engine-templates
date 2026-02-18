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

# 设计文档：三大应用原生 UI 行为对齐审计（64-06）

## 1. 设计目标

- 将“已对齐”的判断从文档描述提升为可查询、可展示的系统状态；
- 保持现有 Suite Hub 导航结构不变，仅增加审计观测信息；
- 与 `64-05` 能力概览统一刷新与治理口径。

## 2. 后端设计

- 控制器：`ArchitectureGovernanceController`
  - 新增路由 `GET /api/v1/architecture/suite-hub/parity-audit`
- 服务：`ArchitectureGovernanceService`
  - 新增 `listSuiteUiParityAudit()`；
  - 返回三大应用原生 UI 行为审计分项与汇总统计。

## 3. 前端设计

- 类型：扩展 `frontend/src/renderer/types/suiteHub.ts`
- 服务：扩展 `frontend/src/renderer/services/suiteHubApiService.ts`
- 视图：增强 `frontend/src/renderer/views/SuiteHubView.vue`
  - 新增“原生 UI 行为对齐审计”面板；
  - 统一 `loadOverviewData()` 同步刷新能力概览与审计概览。

## 4. 验证策略

- 后端定向测试：`ArchitectureGovernanceServiceTest`、`ArchitectureGovernanceControllerTest`
- 前端构建验证：`npm run build:renderer`
- KSE 治理验证：`scene package-validate`、`ontology validate/impact/path`、`templates create-from-spec`、`status`、`doctor`

