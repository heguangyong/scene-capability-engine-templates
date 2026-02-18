---
name: Scene Action Pack Export and Follow-up Linking
category: web-features
description: Template for exporting action packs from action queues and linking follow-up workflows for agents and operators.
difficulty: advanced
tags:
  - scene
  - action-pack
  - export
  - follow-up
  - orchestration
applicable_scenarios:
  - Producing copy-ready action packs for distributed execution
  - Linking action queue outputs to follow-up workflows
  - Preserving runbook and traceability features while exporting action packs
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 需求文档：场景行动包导出与跟进联动

## 概述

在行动队列基础上提供行动包导出能力，方便将执行项分发给主从 agent 或人工跟进。

## 需求 1：行动包结构

### 验收标准

1.1 行动包包含模块信息与行动队列  
1.2 每个行动项包含 owner/SLA/checklist  
1.3 行动包文本适合直接粘贴到协作上下文

## 需求 2：导出与反馈

### 验收标准

2.1 支持一键复制行动包  
2.2 成功和失败反馈语义清晰  
2.3 不影响 Runbook 与路径复制功能

