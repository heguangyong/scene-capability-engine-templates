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

# 设计文档：HiveMind 原生场景闭环（66-01）

## 设计重点

- 以协同场景为中心，避免“表单可用但不可理解”
- 强化失败回退路径，降低操作中断成本
- 保持项目页与审批页上下文一致

## 页面范围

- `ProjectManagementDetailView`
- `WorkflowApprovalCenterView`
- 视需要扩展 `ProjectManagementListView`

## 关键设计

1. 字段级规则区 + 错误分组区 + 恢复建议区。  
2. 审批任务操作失败后展示标准化引导（重载/回退/人工复核）。  
3. 项目状态更新失败可直接跳转审批上下文。  

## 验证

- 页面交互回归（项目 -> 审批 -> 回退）
- `npx kse status --verbose`
- `npx kse doctor --docs`

