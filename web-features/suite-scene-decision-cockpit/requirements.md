---
name: suite-scene-decision-cockpit
category: other
description: Template for Suite Scene Decision Cockpit
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Suite 场景决策驾驶舱（70-01）

## 概述

在场景执行钻取中新增“决策驾驶舱”，将场景风险、审计待补项和依赖复杂度转化为可执行建议。

## 需求 1：决策信号卡片

### 验收标准

1.1 卡片展示标题、优先级、原因、动作  
1.2 支持 `低/中/高` 三类优先级  
1.3 模块切换后信号即时刷新

## 需求 2：信号生成规则

### 验收标准

2.1 按 ER/BR/DL 完整度生成缺口信号  
2.2 按待补检查项和依赖扇出生成风险信号  
2.3 无高风险时生成稳定信号
