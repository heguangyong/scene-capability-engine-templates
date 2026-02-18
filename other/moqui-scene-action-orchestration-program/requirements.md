---
name: moqui-scene-action-orchestration-program
category: other
description: Template for Moqui Scene Action Orchestration Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 场景行动编排计划（71-00）

## 概述

在 `70` 决策驾驶舱基础上，补齐“行动闭环”能力：把决策信号转化为可执行行动队列与行动包导出，支撑多 agent 协作推进。

## 需求 1：行动队列编排

### 验收标准

1.1 按决策信号生成行动队列  
1.2 行动项包含 owner、SLA、检查清单  
1.3 行动项优先级与决策信号一致

## 需求 2：行动包导出

### 验收标准

2.1 支持复制行动包 Markdown  
2.2 行动包包含队列与检查项  
2.3 导出链路具备明确反馈

## 需求 3：治理收口

### 验收标准

3.1 `71-00~71-03` 全部完成并通过 gate  
3.2 scene/package/template/ontology 资产齐全  
3.3 `CURRENT_CONTEXT` 与模板清单同步
