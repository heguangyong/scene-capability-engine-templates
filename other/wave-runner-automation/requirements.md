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

# 需求文档：58-00 Wave Runner 自动化脚本

## 目标
提供一个可复用脚本：先并行执行子 specs，再串行执行 master spec。

## 功能需求
1. 接收子规格列表与主规格参数。
2. 执行 `kse orchestrate run`（sub wave），记录日志。
3. sub 成功后执行 master spec，记录日志。
4. 产出最终 summary JSON。

## 验收标准
- 新增 `scripts/kse-wave-runner.ps1`。
- 支持参数：
  - `-SubSpecs`
  - `-MasterSpec`
  - `-MaxParallel`
  - `-Workspace`
- 能正确生成 `.kiro/config/wave-runner-last.json`。
