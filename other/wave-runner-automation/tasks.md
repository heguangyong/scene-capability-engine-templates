---
name: wave-runner-automation
category: other
description: Template for Wave Runner Automation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：58-00 Wave Runner 自动化脚本

- [x] 1. 实现 wave runner
  - [x] 1.1 新建 `scripts/kse-wave-runner.ps1`
  - [x] 1.2 实现参数解析与校验
  - [x] 1.3 实现 sub wave 执行与日志输出
  - [x] 1.4 实现 master 执行与日志输出
  - [x] 1.5 实现 summary JSON 写入

- [x] 2. 脚本验证
  - [x] 2.1 以 `-WhatIfMode`（只打印命令）运行验证流程
