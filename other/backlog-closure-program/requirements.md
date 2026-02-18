---
name: backlog-closure-program
category: other
description: Template for Backlog Closure Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档

## 简介

针对当前项目仍未达成 `100%` 的历史 Spec，建立统一收敛计划，分批补完任务并确保每个 Spec 具备可追溯证据与发布结论。

## 目标范围

当前待收敛 Spec（8 个）：

- `01-00-customer-requirements`（13/49）
- `02-01-basic-analysis`（17/43）
- `02-06-deep-dive-verification`（19/67）
- `03-00-project-structure-design`（31/62）
- `06-00-groovy-to-java-phase2-p1`（60/78）
- `07-01-moqui-modernization-design`（33/92）
- `07-02-rest-api-layer`（26/40）
- `09-00-pure-restful-backend`（74/84）

## 需求

### 需求 1：建立统一积压清单

1. 系统 SHALL 输出未完成 Spec 清单与完成率快照。  
2. 清单 SHALL 标注每个 Spec 的剩余任务量与优先级。  
3. 清单 SHALL 归档在本 Spec 的 `results/` 目录。

### 需求 2：分波次收敛执行

1. 系统 SHALL 将 8 个 Spec 按依赖关系划分波次执行。  
2. 每个波次完成后 SHALL 更新任务状态与证据链接。  
3. 波次执行 SHALL 避免并发冲突与文档治理回退。

### 需求 3：质量与治理门禁

1. 每个波次结束后 SHALL 执行 `kse status --verbose` 与 `kse doctor --docs`。  
2. 文档治理 SHALL 保持 `Compliant`。  
3. 对于无法在当前波次闭环的项，SHALL 输出阻塞说明与下一步动作。

### 需求 4：上下文连续性

1. 每完成一个阶段 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md`。  
2. 更新内容 SHALL 包含：当前波次、已收敛项、剩余项、下一步。  
3. 上下文 SHALL 支持跨 session 无缝接续。
