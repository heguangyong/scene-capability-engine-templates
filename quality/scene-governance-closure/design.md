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

# 设计文档：场景治理收口模板

## 范围

- scene spec 文档与 scene/package 资产
- `docs/handoffs/evidence/*` 治理证据
- `.kiro/steering/CURRENT_CONTEXT.md` 与模板导出清单

## 关键设计

1. 固化 `build + status + doctor + final-sync` 验证链；  
2. 执行 package/ontology/template 导出并形成 summary；  
3. 同步治理证据与上下文索引，保证下一轮可追溯。  

## 验证

- `npx sce status --verbose`
- `npx sce doctor --docs`
- `powershell -File scripts/kse-final-sync.ps1 -Round <round-id> -OntologyMinAverageScore 70 -OntologyMinValidRate 100`
