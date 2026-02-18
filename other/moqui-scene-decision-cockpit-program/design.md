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

# 设计文档：Moqui 场景决策驾驶舱计划（70-00）

## 设计目标

- 把 SuiteHub 从“执行展示”升级到“执行决策辅助”；
- 输出可复用的 Runbook 文本资产，便于主从 agent 协作；
- 保持前端改动集中在 SuiteHub，不侵入各业务页面。

## 子 Spec 拆分

- `70-01-suite-scene-decision-cockpit`
- `70-02-scene-runbook-export-and-playbook-linking`
- `70-03-scene-decision-governance-closure`

## 方案

1. 在执行钻取区新增决策驾驶舱卡片；  
2. 按场景完整度、审计待补项、依赖扇出计算决策信号；  
3. 生成并复制模块级 Runbook Markdown；  
4. 完成治理与模板收口。  

## 验证

- `npm run build`（frontend）  
- `npx kse status --verbose`  
- `npx kse doctor --docs`  
- `scripts/kse-final-sync.ps1`（70/100 ontology gate）
