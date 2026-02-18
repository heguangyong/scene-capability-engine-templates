---
name: scene-decision-governance-closure
category: other
description: Template for Scene Decision Governance Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：场景决策治理收口（70-03）

## 范围

- 70 系列 spec 文档与 scene/package 资产
- `docs/handoffs/evidence/*` 收口证据
- `.kiro/steering/CURRENT_CONTEXT.md` 与 `docs/handoffs/template-export-list.md`

## 关键设计

1. 固化 `build + status + doctor + final-sync` 验证链；  
2. 对 `70-00~70-03` 执行 package/ontology/template 导出；  
3. 同步治理证据和上下文索引，保持下一轮可无缝衔接。  

## 验证

- `npx kse status --verbose`
- `npx kse doctor --docs`
- `powershell -File scripts/kse-final-sync.ps1 -Round r70-scene-decision-cockpit-20260218 -OntologyMinAverageScore 70 -OntologyMinValidRate 100`
