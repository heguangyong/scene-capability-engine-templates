---
name: kse-agent-spawner-fix
category: other
description: Template for Kse Agent Spawner Fix
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档

## 架构决策

在 Windows 平台改用 stdin 传输完整 bootstrap prompt，避免通过命令行参数直接传递多行文本导致的分词错误与长度限制问题。

## 关键设计点

1. `codex exec` 使用 `-` 作为 prompt 输入标识。  
2. AgentSpawner 在子进程启动后通过 `child.stdin.end(prompt, 'utf-8')` 写入完整 prompt。  
3. 保留 orchestrator 既有参数链（如 `--full-auto`、`--json`、`--sandbox` 及自定义 `codexArgs`）。  
4. 通过脚本化测试与 orchestrate 实测日志验证行为一致性。

## 可观测性

- 保留启动日志、stdout/stderr、编排输出日志作为回归依据。  
- 在 Spec 中归档测试脚本，保证后续版本回归可复现。
