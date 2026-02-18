---
name: multi-agent-parallel-validation
category: other
description: Template for Multi Agent Parallel Validation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：55-00 多 Agent 并行验证主控

## 三层结构
- 执行层：`kse orchestrate run` 并行执行 sub specs。
- 管理层：`kse collab init/assign/status` 管理主从拓扑。
- 留痕层：统一产出验证报告，便于复盘与复用。

## 主从关系
- Master: `{{SPEC_NAME}}`
- Sub:
  - `56-00-orchestrate-live-monitor`
  - `57-00-agent-registry-maintenance`
  - `58-00-wave-runner-automation`

## 输出物
- `reports/parallel-validation-report.md`
