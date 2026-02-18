---
name: moqui-scene-action-orchestration-program
category: other
description: Template for Moqui Scene Action Orchestration Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 场景行动编排计划（71-00）

## 设计目标

- 把场景决策建议转化为行动执行项；
- 形成可复制、可分发的行动包文本；
- 保持 SuiteHub 作为统一场景治理入口。

## 子 Spec 拆分

- `71-01-suite-scene-action-queue-orchestration`
- `71-02-scene-action-pack-export-and-followup-linking`
- `71-03-scene-action-governance-closure`

## 方案

1. 基于决策信号生成行动队列（owner/SLA/checklist）；  
2. 在执行钻取区新增行动队列与导出入口；  
3. 输出行动包 Markdown 供主从 agent 协作使用；  
4. 完成治理与模板收口。  

## 验证

- `npm run build`（frontend）  
- `npx kse status --verbose`  
- `npx kse doctor --docs`  
- `scripts/kse-final-sync.ps1`（70/100 ontology gate）


## Ontology Model (Backfilled)

### Entities
- **MoquiSceneActionOrchestrationProgramRecord**: Core domain record for Moqui Scene Action Orchestration Program scenarios.
- **MoquiSceneActionOrchestrationProgramProcess**: Process context handling lifecycle transitions.
- **MoquiSceneActionOrchestrationProgramAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **MoquiSceneActionOrchestrationProgramRecord** 1:N **MoquiSceneActionOrchestrationProgramProcess** (lifecycle orchestration).
- **MoquiSceneActionOrchestrationProgramProcess** 1:N **MoquiSceneActionOrchestrationProgramAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
