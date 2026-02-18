---
name: moqui-scene-composition-visualization-program
category: other
description: Template for Moqui Scene Composition Visualization Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 场景编排可视化计划（68-00）

## 概述

本 Spec 在 `67` 可操作性加固基础上，继续补齐“场景可理解性”短板：让用户从 SuiteHub 直接看清每个套件的场景组合、规则约束与决策逻辑。

## 需求 1：场景组合画布

### 验收标准

1.1 SuiteHub 展示按套件组织的场景组合画布  
1.2 每个模块可见 `ER/BR/DL` 三层摘要  
1.3 用户可直接定位模块场景入口

## 需求 2：场景交互增强

### 验收标准

2.1 支持按套件过滤场景模块  
2.2 支持场景关键流步骤可视化  
2.3 支持模块间依赖提示

## 需求 3：治理收口

### 验收标准

3.1 `68-00~68-03` 全部完成并通过 gate  
3.2 scene/package/template/ontology 资产齐全  
3.3 `CURRENT_CONTEXT` 与模板清单同步

