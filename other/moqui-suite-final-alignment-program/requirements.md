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

# 需求文档：Moqui 三套件最终对齐收官计划（65-00）

## 概述

本 Spec 用于在 `64-00~64-07` 的基础上，完成 Moqui 三大套件（`HiveMind`、`PopCommerce`、`MarbleERP`）与 `331-poc` 的最终对齐收官：

1. 做完“菜单 -> 页面 -> 表单 -> API -> 规则”全链路缺口审计；
2. 通过主从 spec 多 agent 并行推进缺口清零；
3. 形成可直接模板化、可持续守护的收官资产。

---

## 需求 1：形成三套件最终缺口审计基线

**用户故事**：作为项目 owner，我希望明确知道仍未对齐的能力项与优先级，避免“看起来完成但体验未闭环”。

### 验收标准

1.1 产出三套件最终审计清单（P0/P1/P2）  
1.2 每个缺口项包含 `当前表现/目标表现/证据路径/归属 spec`  
1.3 审计清单覆盖 ontology 三层（实体关系、业务规则、决策逻辑）

---

## 需求 2：建立主从 spec 的并行执行编排

**用户故事**：作为执行者，我希望用一条稳定流程并行推进子 spec，再自动回收总控 spec。

### 验收标准

2.1 建立 `65-01/65-02/65-03` 子 spec（分别对应三套件）  
2.2 建立 `65-00` 总控 spec 统一收口  
2.3 提供一键执行脚本（含并发降级与 429 回退）

---

## 需求 3：将治理 gate 固化为必经流程

**用户故事**：作为治理负责人，我希望每轮执行都自动经过文档与 ontology gate，确保质量不回退。

### 验收标准

3.1 固化 `kse status --verbose`、`kse doctor --docs`  
3.2 固化 `scene package-publish-batch --from-331 --dry-run --ontology-min-average-score 70 --ontology-min-valid-rate 100`  
3.3 所有 gate 证据落盘到 `docs/handoffs/evidence`

---

## 需求 4：收官成果可模板化与可复用

**用户故事**：作为 KSE 协同使用者，我希望三套件最终对齐方案可作为模板库资产复用。

### 验收标准

4.1 子 spec 与总控 spec 的场景资产具备模板导出条件  
4.2 输出多 agent 执行手册与交付清单  
4.3 `CURRENT_CONTEXT` 与 handoff 清单同步更新

---
