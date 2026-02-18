---
name: moqui-native-scene-closure-program
category: other
description: Template for Moqui Native Scene Closure Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 原生场景闭环计划（66-00）

## 1. 设计目标

- 以“原生场景闭环”而非“单页功能补丁”推进下一轮补齐；
- 优先补齐高密度表单语义、失败恢复路径与跨页面场景连续性；
- 保持 KSE 主从执行、治理校验和模板化输出的连续链路。

## 2. 输入来源

- `docs/legacy-screen-engine/README.md`（原生 Screen 规模与结构）
- `.kiro/specs/64-00~64-03/docs/*-map.md`（三套件映射基线）
- `.kiro/specs/65-00~65-03/*`（最终对齐收官成果）
- `frontend/src/renderer/config/menu-config.js`（当前 IA 与入口组织）

## 3. 架构方案

### 3.1 Spec 拆分

- `66-01-hivemind-native-scene-closure`
- `66-02-popcommerce-native-scene-closure`
- `66-03-marbleerp-native-scene-closure`
- `66-00` 负责总控与治理收口

### 3.2 执行流

1) 先完成 `v2` 差距审计和优先级分组；  
2) 子 spec 并行执行补齐；  
3) 主 spec 汇总证据并执行治理 gate。

### 3.3 输出资产

- 差距审计：`docs/moqui-native-gap-audit-v2.md`
- 套件执行证据：`docs/handoffs/evidence/orchestration/*`
- 模板与 ontology 证据：`docs/handoffs/evidence/templates/*`、`docs/handoffs/evidence/ontology/*`

## 4. 关键约束

- 保持低并发防止 429（沿用 `wave-runner` 策略）
- 所有结论必须有证据文件，不以口头状态替代
- 每个子 spec 改动必须可映射到 ontology 三层

## 5. 验证与收口

- `npx kse status --verbose`
- `npx kse doctor --docs`
- `powershell -File scripts/kse-final-sync.ps1 -Root . -Round <round> -OntologyMinAverageScore 70 -OntologyMinValidRate 100`

