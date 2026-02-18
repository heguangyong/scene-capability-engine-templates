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

# 任务清单：57-00 Agent Registry 维护脚本

- [x] 1. 实现维护脚本
  - [x] 1.1 新建 `scripts/kse-{{SPEC_NAME}}.ps1`
  - [x] 1.2 实现 stale agents 识别逻辑
  - [x] 1.3 实现 dry-run / apply 分支
  - [x] 1.4 实现 registry 备份与回写
  - [x] 1.5 实现可选 `kse lock cleanup`

- [x] 2. 脚本验证
  - [x] 2.1 运行 dry-run 命令并确认成功
