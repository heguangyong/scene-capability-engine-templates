---
name: moqui-capability-itemized-parity-matrix
category: other
description: Template for Moqui Capability Itemized Parity Matrix
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：能力项级对齐矩阵（62-01）

## 1. 输入
- `01-00` 需求域
- `60-00` 对齐基线
- 现有代码路由/控制器/服务

## 2. 数据模型
- `capability_id`
- `domain/subdomain`
- `status`
- `evidence_paths`
- `gap_note`
- `target_spec`

## 3. 输出
- `docs/moqui-capability-itemized-matrix.md`
- `docs/moqui-capability-gap-backlog.md`

## 4. DoD
- 三态判定覆盖率 100%
- 证据路径可追溯
- 缺口可被后续 spec 消化
