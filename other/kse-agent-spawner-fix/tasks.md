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

# Tasks: 54-00 KSE Agent Spawner Fix

## 任务列表

- [x] 1 复现并定位 Windows 启动失败
  - [x] 1.1 收集 orchestrate 失败日志
  - [x] 1.2 确认参数分词与 prompt 传输根因

- [x] 2 实施 AgentSpawner 热修复
  - [x] 2.1 切换为 stdin prompt 传输方案
  - [x] 2.2 保持 orchestrator 参数兼容性

- [x] 3 完成回归验证
  - [x] 3.1 执行脚本化 self-test
  - [x] 3.2 验证 `kse orchestrate run` 可用性

- [x] 4 归档证据与结论
  - [x] 4.1 输出 bug/hotfix 文档
  - [x] 4.2 归档日志与测试脚本
