---
name: scene-visualization-governance-closure
category: other
description: Template for Scene Visualization Governance Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：场景可视化治理收口（68-03）

## 概述

负责 `68` 批次的治理命令、证据落盘、模板导出与上下文同步。

## 需求 1：治理 gate

### 验收标准

1.1 `status/doctor/final-sync` 全通过  
1.2 package/ontology 严格校验通过  
1.3 形成批次 summary 证据

## 需求 2：模板与上下文同步

### 验收标准

2.1 导出 `68-00~68-03` 模板  
2.2 更新模板清单计数  
2.3 更新 `CURRENT_CONTEXT`

