---
name: scene-action-pack-export-and-followup-linking
category: other
description: Template for Scene Action Pack Export And Followup Linking
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：场景行动包导出与跟进联动（71-02）

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 基于 `decisionActionQueue` 组装行动包 Markdown；  
2. 使用统一剪贴板写入函数输出导出反馈；  
3. 保持导出逻辑与运行中的模块上下文一致。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 可复制行动包并显示通知


## Ontology Model (Backfilled)

### Entities
- **SceneActionPackExportAndFollowupLinkingRecord**: Core domain record for Scene Action Pack Export And Followup Linking scenarios.
- **SceneActionPackExportAndFollowupLinkingProcess**: Process context handling lifecycle transitions.
- **SceneActionPackExportAndFollowupLinkingAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **SceneActionPackExportAndFollowupLinkingRecord** 1:N **SceneActionPackExportAndFollowupLinkingProcess** (lifecycle orchestration).
- **SceneActionPackExportAndFollowupLinkingProcess** 1:N **SceneActionPackExportAndFollowupLinkingAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
