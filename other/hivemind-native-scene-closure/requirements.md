---
name: hivemind-native-scene-closure
category: other
description: Template for Hivemind Native Scene Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：HiveMind 原生场景闭环（66-01）

## 概述

围绕 `Project -> WBS -> Task -> ApprovalTask` 主链，按 Moqui 原生协同语义继续补齐页面深度：

1. 强化高密度协同表单规则可见性；
2. 强化审批与任务联动的恢复路径；
3. 强化项目上下文在跨页面跳转中的连续性。

## 需求 1：高密度协同表单可解释

### 验收标准

1.1 关键字段约束（必填/默认值/禁用条件）可见  
1.2 校验失败后给出可执行恢复路径  
1.3 表单操作与审批状态联动关系可见

## 需求 2：审批联动恢复路径闭环

### 验收标准

2.1 任务通过/驳回失败时提供重试与回退建议  
2.2 项目状态变更失败可回链审批上下文  
2.3 关键失败场景具备统一提示语义

## 需求 3：治理与证据闭环

### 验收标准

3.1 变更可映射 ontology 三层  
3.2 `status/doctor` 通过  
3.3 证据落盘并同步总控 spec

