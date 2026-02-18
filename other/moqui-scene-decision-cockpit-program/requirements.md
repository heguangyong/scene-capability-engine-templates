---
name: moqui-scene-decision-cockpit-program
category: other
description: Template for Moqui Scene Decision Cockpit Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 场景决策驾驶舱计划（70-00）

## 概述

在 `69` 场景执行钻取基础上，补齐“执行建议与决策优先级”能力：让 SuiteHub 不仅展示步骤，还能给出决策信号与可复制 Runbook。

## 需求 1：决策驾驶舱

### 验收标准

1.1 在执行钻取区域展示决策信号卡  
1.2 信号包含优先级、原因与建议动作  
1.3 信号可随模块切换实时更新

## 需求 2：Runbook 导出

### 验收标准

2.1 支持复制当前模块场景 Runbook（Markdown）  
2.2 Runbook 包含执行步骤、依赖路径、决策信号  
2.3 导出能力不影响现有导航和交互

## 需求 3：治理收口

### 验收标准

3.1 `70-00~70-03` 全部完成并通过 gate  
3.2 scene/package/template/ontology 资产齐全  
3.3 `CURRENT_CONTEXT` 与模板清单同步
