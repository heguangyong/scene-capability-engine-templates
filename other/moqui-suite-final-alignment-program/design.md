---
name: moqui-suite-final-alignment-program
category: other
description: Template for Moqui Suite Final Alignment Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 三套件最终对齐收官计划（65-00）

## 1. 设计目标

- 将“功能已实现”升级为“场景可理解、流程可执行、证据可复盘”。
- 以三套件视角组织收官，而非仅按技术模块分散推进。
- 使用 KSE 主从编排保障连续执行，降低 session 中断影响。

## 2. 信息来源

- `docs/legacy-screen-engine/README.md`（原生 Screen 规模与来源）
- `.kiro/specs/64-00~64-03/docs/*-suite-map.md`（三套件映射基线）
- `frontend/src/renderer/config/menu-config.js`（当前 IA 与入口组织）
- `docs/handoffs/handoff-manifest.json`（治理与证据主清单）

## 3. 架构方案

### 3.1 Spec 拆分

- `65-01-hivemind-final-alignment`：项目协作场景闭环
- `65-02-popcommerce-final-alignment`：交易履约场景闭环
- `65-03-marbleerp-final-alignment`：计划执行核算场景闭环
- `{{SPEC_NAME}}`：总控整合、审计复盘、模板收口

### 3.2 执行流

1) 子 spec 并行执行（maxParallel=2/3）  
2) 总控 spec 统一收口  
3) 执行 `final-sync` + ontology publish gate（70/100）

### 3.3 输出资产

- 最终缺口审计：`docs/moqui-suite-final-gap-audit.md`
- 多 agent 手册：`docs/handoffs/65-suite-multi-agent-playbook.md`
- 批跑脚本：`scripts/kse-suite65-wave.ps1`

## 4. 关键约束

- 高并发可能触发 429，必须保留降并发回退。
- 任何收官结论必须落证据文件，不使用口头状态。
- 优先修复场景可理解性（菜单/页面/表单/规则可见性）再做细节优化。

## 5. 验证与收口

- `npx kse status --verbose`
- `npx kse doctor --docs`
- `npx kse scene package-publish-batch --from-331 --dry-run --ontology-min-average-score 70 --ontology-min-valid-rate 100 --json`

