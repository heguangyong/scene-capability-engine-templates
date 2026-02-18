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

# 设计文档：58-00 Wave Runner 自动化脚本

## 执行流程
1. 参数校验
2. 执行 sub wave（并行）
3. 判断退出码
4. 若成功，执行 master
5. 写入 summary JSON

## 日志策略
- sub log: `.kiro/config/wave-runner-sub-<timestamp>.log`
- main log: `.kiro/config/wave-runner-main-<timestamp>.log`
- summary: `.kiro/config/wave-runner-last.json`
