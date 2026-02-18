---
name: scene-evidence-governance-closure
category: other
description: Template for Scene Evidence Governance Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：场景证据治理收口（67-03）

## 概述

本 Spec 负责 `67` 批次治理收口、证据落盘、模板导出与上下文同步。

## 需求 1：治理 Gate 收口

### 验收标准

1.1 `status/doctor/final-sync` 通过  
1.2 ontology 发布 gate 通过（平均分阈值与有效率阈值）  
1.3 形成可追溯 summary 证据文件

## 需求 2：模板导出与清单更新

### 验收标准

2.1 `67-00~67-03` 模板导出成功  
2.2 模板清单统计更新  
2.3 导出日志与汇总文件落盘

## 需求 3：上下文同步

### 验收标准

3.1 `CURRENT_CONTEXT.md` 更新到最新阶段  
3.2 补充 `67` 批次完成记录  
3.3 与 handoff 证据路径一致

