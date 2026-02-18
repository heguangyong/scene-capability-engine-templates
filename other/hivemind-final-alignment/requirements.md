---
name: hivemind-final-alignment
category: other
description: Template for Hivemind Final Alignment
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：HiveMind 最终对齐（65-01）

## 概述

围绕 `Project -> WBS -> Task -> ApprovalTask` 主链，补齐 HiveMind 视角的页面组织、表单规则可见性与协同闭环体验。

## 需求 1：项目协作主链可解释

### 验收标准

1.1 项目/WBS/任务页面可展示当前阶段与下一步动作  
1.2 审批节点与任务状态关系在页面中可见  
1.3 失败/驳回路径可回溯到任务上下文

## 需求 2：高密度表单规则补齐

### 验收标准

2.1 补齐关键字段约束、默认值与禁用条件展示  
2.2 表单校验提示与业务规则保持一致  
2.3 关键操作具备审计提示

## 需求 3：治理与证据闭环

### 验收标准

3.1 变更落入 scene 资产与证据文档  
3.2 `status/doctor` 通过  
3.3 ontology gate 不退化

