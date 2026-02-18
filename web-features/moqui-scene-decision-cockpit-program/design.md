---
name: moqui-scene-decision-cockpit-program
category: other
description: Template for Moqui Scene Decision Cockpit Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 场景决策驾驶舱计划（70-00）

## 设计目标

- 把 SuiteHub 从“执行展示”升级到“执行决策辅助”；
- 输出可复用的 Runbook 文本资产，便于主从 agent 协作；
- 保持前端改动集中在 SuiteHub，不侵入各业务页面。

## 子 Spec 拆分

- `70-01-suite-scene-decision-cockpit`
- `70-02-scene-runbook-export-and-playbook-linking`
- `70-03-scene-decision-governance-closure`

## 方案

1. 在执行钻取区新增决策驾驶舱卡片；  
2. 按场景完整度、审计待补项、依赖扇出计算决策信号；  
3. 生成并复制模块级 Runbook Markdown；  
4. 完成治理与模板收口。  

## 验证

- `npm run build`（frontend）  
- `npx kse status --verbose`  
- `npx kse doctor --docs`  
- `scripts/kse-final-sync.ps1`（70/100 ontology gate）


## Ontology Model (Backfilled)

### Entities
- **MoquiSceneDecisionCockpitProgramRecord**: Core domain record for Moqui Scene Decision Cockpit Program scenarios.
- **MoquiSceneDecisionCockpitProgramProcess**: Process context handling lifecycle transitions.
- **MoquiSceneDecisionCockpitProgramAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **MoquiSceneDecisionCockpitProgramRecord** 1:N **MoquiSceneDecisionCockpitProgramProcess** (lifecycle orchestration).
- **MoquiSceneDecisionCockpitProgramProcess** 1:N **MoquiSceneDecisionCockpitProgramAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
