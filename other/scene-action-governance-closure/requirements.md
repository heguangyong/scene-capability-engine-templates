---
name: scene-action-governance-closure
category: other
description: Template for Scene Action Governance Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：场景行动治理收口（71-03）

## 概述

完成 71 批次治理闭环，确保行动队列与行动包导出在文档、模板、证据层可追溯。

## 需求 1：治理 gate

### 验收标准

1.1 执行 `status` / `doctor` / `final-sync`  
1.2 scene package 与 ontology 校验通过  
1.3 形成 71 批次治理证据

## 需求 2：模板与上下文同步

### 验收标准

2.1 导出 `71-00~71-03` 模板  
2.2 更新 `CURRENT_CONTEXT` 与模板清单  
2.3 证据路径纳入 SuiteHub 场景追溯
