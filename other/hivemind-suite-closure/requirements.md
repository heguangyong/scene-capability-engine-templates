---
name: hivemind-suite-closure
category: other
description: Template for Hivemind Suite Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：HiveMind 套件闭环（64-01）

## 概述

本 Spec 聚焦 HiveMind（项目管理与协同）能力在 `331-poc` 的组织映射与持续闭环，确保项目/WBS/审批/协作能力在新架构中可复盘、可模板化、可多 agent 并行执行。

## 需求 1：能力映射闭环

### 验收标准

1.1 输出 HiveMind 核心能力到 331 模块/路由映射  
1.2 输出实体关系、业务规则、决策逻辑三层映射  
1.3 映射文档可追溯到现有 Spec 与代码入口

## 需求 2：场景合同闭环

### 验收标准

2.1 提供 `scene.yaml + scene-package.json`  
2.2 包含 `capability_contract + governance_contract`  
2.3 可通过 `package-validate` 与 `ontology validate`

## 需求 3：模板与治理闭环

### 验收标准

3.1 可执行模板导出  
3.2 可通过 `kse status --verbose` 与 `kse doctor --docs`  
3.3 产物可纳入 handoff 索引

