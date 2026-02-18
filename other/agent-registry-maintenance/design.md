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
