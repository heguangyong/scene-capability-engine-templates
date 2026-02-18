---
name: agent-registry-maintenance
category: other
description: Template for Agent Registry Maintenance
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：57-00 Agent Registry 维护脚本

## 参数
- `-Workspace`：项目根目录
- `-StaleMinutes`：过期阈值，默认 30
- `-Apply`：应用修改
- `-LockCleanup`：apply 后执行 lock cleanup

## 安全策略
- 默认 dry-run
- apply 前自动备份 registry 到 `.kiro/config/backups/`
- 回写 JSON 采用 UTF-8 无 BOM


## Ontology Model (Backfilled)

### Entities
- **AgentRegistryMaintenanceRecord**: Core domain record for Agent Registry Maintenance scenarios.
- **AgentRegistryMaintenanceProcess**: Process context handling lifecycle transitions.
- **AgentRegistryMaintenanceAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **AgentRegistryMaintenanceRecord** 1:N **AgentRegistryMaintenanceProcess** (lifecycle orchestration).
- **AgentRegistryMaintenanceProcess** 1:N **AgentRegistryMaintenanceAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
