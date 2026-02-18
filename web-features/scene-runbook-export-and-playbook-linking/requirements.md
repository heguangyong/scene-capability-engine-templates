---
name: scene-runbook-export-and-playbook-linking
category: other
description: Template for Scene Runbook Export And Playbook Linking
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：场景 Runbook 导出与联动（70-02）

## 概述

将当前模块执行语义生成 Runbook Markdown，支持复制并用于主从 agent 或人工交接。

## 需求 1：Runbook 内容结构

### 验收标准

1.1 包含基础信息（套件/路由/场景）  
1.2 包含执行步骤矩阵摘要  
1.3 包含依赖路径与决策信号

## 需求 2：导出与通知

### 验收标准

2.1 提供复制 Runbook 按钮  
2.2 成功/失败通知语义明确  
2.3 不影响现有追溯路径复制能力
