---
name: suite-hub-maturity-and-traceability
category: other
description: Template for Suite Hub Maturity And Traceability
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：SuiteHub 成熟度与追溯增强（67-01）

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`
- `frontend/src/renderer/types/suiteHub.ts`（仅类型补充）

## 关键设计

1. 在 capability/parity 概览基础上推导成熟度评分；  
2. 按统一阈值推导风险等级（low/medium/high）；  
3. 新增追溯板块，按套件展示 Spec/Evidence 路径并支持复制。  

## 评分与分级策略

- `maturityScore = coverageRate * 0.6 + passRate * 0.4`
- 风险分级：
  - `high`: 评分 < 85 或待补项 >= 3
  - `medium`: 评分 < 92 或待补项 > 0
  - `low`: 其余

## 验证

- `npm run build`（frontend）
- 手动检查 `/suite-hub`：
  - 成熟度与风险卡片可见
  - 路径复制交互可用



## Ontology Model (Backfilled)

### Entities
- **SuiteHubMaturityAndTraceabilityRecord**: Core domain record for Suite Hub Maturity And Traceability scenarios.
- **SuiteHubMaturityAndTraceabilityProcess**: Process context handling lifecycle transitions.
- **SuiteHubMaturityAndTraceabilityAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **SuiteHubMaturityAndTraceabilityRecord** 1:N **SuiteHubMaturityAndTraceabilityProcess** (lifecycle orchestration).
- **SuiteHubMaturityAndTraceabilityProcess** 1:N **SuiteHubMaturityAndTraceabilityAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
