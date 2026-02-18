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

# 需求文档：57-00 Agent Registry 维护脚本

## 目标
提供一个安全脚本，用于检查并清理过期 Agent 记录，并可触发 lock cleanup。

## 功能需求
1. 读取 `.kiro/config/agent-registry.json`。
2. 根据 `lastHeartbeat` 与阈值分钟数识别 stale agents。
3. 支持 dry-run 与 apply 两种模式。
4. apply 模式下：
   - 备份原 registry 文件
   - 删除 stale agents 并回写
   - 可选调用 `kse lock cleanup`

## 验收标准
- 新增 `scripts/kse-{{SPEC_NAME}}.ps1`。
- dry-run 可执行，不破坏现有数据。
