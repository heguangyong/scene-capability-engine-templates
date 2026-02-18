---
name: orchestrate-live-monitor
category: other
description: Template for Orchestrate Live Monitor
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：56-00 编排实时监控脚本

## 目标
提供一个可直接运行的 PowerShell 监控脚本，实时观察 KSE 编排执行状态。

## 功能需求
1. 读取 `.kiro/config/orchestration-status.json` 并显示核心字段：
   - status / completedSpecs / failedSpecs / runningSpecs / currentBatch / totalBatches
2. 显示当前相关进程计数：
   - `kse orchestrate run` 进程数
   - `codex exec --full-auto` 进程数
3. 支持单次与持续监控两种模式。

## 验收标准
- 新增脚本 `scripts/kse-orchestrate-monitor.ps1`。
- 命令 `powershell -ExecutionPolicy Bypass -File scripts/kse-orchestrate-monitor.ps1 -Once` 可运行成功。
