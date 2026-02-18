---
name: moqui-three-suite-capability-closure
category: other
description: Template for Moqui Three Suite Capability Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 三大应用能力闭环（64-00）

## 概述

本 Spec 用于把 Moqui 原生三大应用（`HiveMind`、`PopCommerce`、`MarbleERP`）与 `331-poc` 当前能力组织做一层“可执行的闭环映射”，并输出可直接被 KSE 复用的 ontology/scene 资产。

---

## 需求 1：建立三大应用到 331 能力映射基线

**用户故事**：作为项目负责人，我希望明确看到三大应用在 331 的落点，避免功能重复和遗漏。

### 验收标准

1.1 形成三大应用能力映射表（模块、路由、关联 Spec、证据路径）  
1.2 映射表覆盖实体关系、业务规则、决策逻辑三层  
1.3 输出可复盘文档并纳入当前 Spec 文档目录

---

## 需求 2：完成菜单 IA 与三大应用语义对齐

**用户故事**：作为业务用户，我希望菜单组织与业务套件语义一致，降低学习成本。

### 验收标准

2.1 明确每个应用在当前一级/二级菜单中的归属与入口策略  
2.2 明确跨域模块（如审批、报表、迁移验证）的共享规则  
2.3 形成“当前已对齐 / 待增强”清单，供后续批次直接执行

---

## 需求 3：形成 KSE ontology 可内化场景合同

**用户故事**：作为 KSE 协同执行者，我希望三大应用的场景资产可直接用于模板化和多 agent 编排。

### 验收标准

3.1 提供 `scene.yaml + scene-package.json`  
3.2 场景合同包含 `capability_contract` 与 `governance_contract`  
3.3 场景合同可通过 `package-validate` 与 `ontology validate`

---

## 需求 4：与持续守护总线衔接

**用户故事**：作为执行 owner，我希望本 Spec 可直接接入 62/63 持续守护链路。

### 验收标准

4.1 回填 `.kiro/steering/CURRENT_CONTEXT.md`  
4.2 回填 `docs/handoffs/handoff-manifest.json`  
4.3 `kse status --verbose` 与 `kse doctor --docs` 通过

