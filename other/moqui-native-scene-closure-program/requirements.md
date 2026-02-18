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

# 需求文档：Moqui 原生场景闭环计划（66-00）

## 概述

本 Spec 用于在 `65-00~65-03` 收官后，继续按 Moqui 原生场景组织方式推进 331-poc 深度对齐：

1. 基于原生 Screen 归档完成第二轮差距审计；
2. 按三大套件拆分为可并行执行的子 spec；
3. 将补齐成果沉淀为可复用模板与 ontology 资产。

---

## 需求 1：形成 Moqui 原生场景差距审计 v2

**用户故事**：作为项目 owner，我希望看到“原生场景组织 vs 当前页面组织”的第二轮差距和优先级。

### 验收标准

1.1 输出 `v2` 差距审计（含 HiveMind/PopCommerce/MarbleERP）  
1.2 每个差距项包含 `当前表现/目标表现/优先级/落地页`  
1.3 每个差距项可映射到 ontology 三层（实体关系、业务规则、决策逻辑）

---

## 需求 2：建立三套件原生场景补齐子 spec

**用户故事**：作为执行者，我希望按套件维度并行推进，避免跨域任务相互阻塞。

### 验收标准

2.1 建立 `66-01`（HiveMind）  
2.2 建立 `66-02`（PopCommerce）  
2.3 建立 `66-03`（MarbleERP）

---

## 需求 3：形成主从并行执行与证据收口流程

**用户故事**：作为治理负责人，我希望每轮推进都能自动复核并留痕。

### 验收标准

3.1 子 spec 可并行执行，主 spec 可统一收口  
3.2 执行后通过 `status/doctor/final-sync`  
3.3 证据落盘到 `docs/handoffs/evidence`

---

## 需求 4：补齐成果可模板化交付

**用户故事**：作为 KSE 协同使用者，我希望补齐成果可导出模板并用于后续批次。

### 验收标准

4.1 新增完成态 spec 支持 `templates create-from-spec`  
4.2 scene/package 资产可通过 ontology 校验  
4.3 handoff 清单与 CURRENT_CONTEXT 同步更新

---
