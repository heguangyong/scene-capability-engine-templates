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

# 需求文档

## 简介

修复 KSE 在 Windows 平台通过 `kse orchestrate` 拉起 Codex Agent 的稳定性问题，消除 prompt 传参与命令拼接导致的执行失败，恢复多 Agent 编排能力。

## 需求

### 需求 1：Windows AgentSpawner 启动可靠性

1. THE AgentSpawner SHALL 在 Windows 下稳定启动 Codex 子进程。  
2. THE prompt 传输 SHALL 使用避免参数分词/长度限制的安全通道。  
3. WHEN orchestration 执行时，THE command line SHALL 保持可预期且可审计。

### 需求 2：编排能力可验证

1. THE fix SHALL 支持 `kse orchestrate run` 正常触发 agent 执行。  
2. THE 执行过程 SHALL 具备日志与输出证据可追溯。  
3. THE 修复结果 SHALL 在 Spec 中沉淀验证脚本与报告。
