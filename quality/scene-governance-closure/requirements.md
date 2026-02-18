---
name: Scene Governance Closure
category: quality
description: Template for scene capability governance closure with status/doctor/final-sync gates, ontology checks, template export, and context synchronization.
difficulty: advanced
tags:
  - scene
  - governance
  - ontology
  - final-sync
  - evidence
applicable_scenarios:
  - Closing a scene capability delivery batch with reproducible governance gates
  - Producing orchestration, ontology, and template evidence packages
  - Synchronizing CURRENT_CONTEXT and template export manifests for next-round continuity
author: FallingAKS
created_at: 2026-02-18
updated_at: 2026-02-18
version: 1.0.0
kse_version: 3.0.2
---
# 需求文档：场景治理收口模板

## 概述

用于收口任意 scene 批次交付，确保状态检查、文档治理、ontology gate、模板导出与上下文同步形成可复盘闭环。

## 需求 1：治理 gate 一次通过

### 验收标准

1.1 执行 status / doctor / inal-sync 全通过  
1.2 scene package 与 ontology 校验通过  
1.3 形成批次级 summary 证据

## 需求 2：模板与证据资产同步

### 验收标准

2.1 导出本批次模板并更新模板清单  
2.2 汇总 orchestration / ontology / templates 证据  
2.3 证据路径可被追溯并可复用

## 需求 3：上下文延续

### 验收标准

3.1 更新 CURRENT_CONTEXT 与交付索引  
3.2 记录下一轮推进建议与风险  
3.3 保证下一轮可无缝接续
