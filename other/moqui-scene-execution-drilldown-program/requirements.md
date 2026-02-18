---
name: moqui-scene-execution-drilldown-program
category: other
description: Template for Moqui Scene Execution Drilldown Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 场景执行钻取计划（69-00）

## 概述

在 `68` 场景画布可视化基础上，补齐“怎么执行”的可操作性：让用户从 SuiteHub 看到每个模块的执行阶段、规则约束、决策逻辑与跨套件依赖路径。

## 需求 1：执行路径可视化

### 验收标准

1.1 在 SuiteHub 展示模块级执行钻取面板  
1.2 每个步骤同步显示 ER/BR/DL 三元信息  
1.3 用户可从步骤直接进入业务模块

## 需求 2：跨套件依赖钻取

### 验收标准

2.1 显示模块依赖路径与目标套件  
2.2 支持依赖链路一键跳转  
2.3 套件过滤后钻取范围自动收敛

## 需求 3：治理收口

### 验收标准

3.1 `69-00~69-03` 全部完成并通过 gate  
3.2 scene/package/template/ontology 资产齐全  
3.3 `CURRENT_CONTEXT` 与模板清单同步
