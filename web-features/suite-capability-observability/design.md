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

# 设计文档：三大应用能力对齐可观测（64-05）

## 1. 设计目标

- 为 Suite Hub 提供可机器消费的能力对齐数据源；
- 将 Moqui 对齐状态从文档侧提升到 UI 即时可见；
- 保持现有导航结构不变，仅新增观测层能力。

## 2. 后端设计

- 控制器：`backend/app/src/main/java/org/moqui/rest/controller/ArchitectureGovernanceController.java`
  - 新增路由：`GET /api/v1/architecture/suite-hub/capability-overview`
- 服务：`backend/app/src/main/java/org/moqui/rest/service/ArchitectureGovernanceService.java`
  - 新增 `listSuiteCapabilityOverview()`；
  - 输出三大应用能力统计与总览汇总。

## 3. 前端设计

- 新增类型：`frontend/src/renderer/types/suiteHub.ts`
- 新增 API 服务：`frontend/src/renderer/services/suiteHubApiService.ts`
- 视图增强：`frontend/src/renderer/views/SuiteHubView.vue`
  - 新增能力总览面板；
  - 新增覆盖率标签与刷新动作；
  - 增加错误提示与加载态处理。

## 4. 测试策略

- 后端：扩展 `ArchitectureGovernanceServiceTest` 与 `ArchitectureGovernanceControllerTest`；
- 前端：通过 `build:renderer` 做编译级回归验证；
- 治理：执行 `kse status --verbose` 与 `kse doctor --docs`。



## Ontology Model (Backfilled)

### Entities
- **SuiteCapabilityObservabilityRecord**: Core domain record for Suite Capability Observability scenarios.
- **SuiteCapabilityObservabilityProcess**: Process context handling lifecycle transitions.
- **SuiteCapabilityObservabilityAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **SuiteCapabilityObservabilityRecord** 1:N **SuiteCapabilityObservabilityProcess** (lifecycle orchestration).
- **SuiteCapabilityObservabilityProcess** 1:N **SuiteCapabilityObservabilityAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
