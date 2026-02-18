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

# 任务清单：56-00 编排实时监控脚本

- [x] 1. 实现监控脚本
  - [x] 1.1 新建 `scripts/kse-orchestrate-monitor.ps1`
  - [x] 1.2 实现 status 文件读取与字段展示
  - [x] 1.3 实现 orchestrate/codex 进程计数展示
  - [x] 1.4 支持 `-Once` 与循环模式

- [x] 2. 脚本验证
  - [x] 2.1 运行单次监控命令验证可执行
