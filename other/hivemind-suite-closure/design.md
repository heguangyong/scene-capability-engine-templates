---
name: hivemind-suite-closure
category: other
description: Template for Hivemind Suite Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：HiveMind 套件闭环（64-01）

## 1. 目标

- 对齐 HiveMind 的“项目 + 协同 + 审批”语义到 331 领域导航。
- 把套件语义转成 KSE 可执行 scene 合同。

## 2. 组织策略

- 入口模块：`project-mgmt`、`party`、`workflow-approval`、`reporting-ops`
- 关键路由：`/project-mgmt`、`/party`、`/workflow-approval`
- 关联总线：`60-06`、`60-10`、`62-03`、`63-01`

## 3. ontology 三层

1. 实体关系：`Project -> WBS -> Task -> ApprovalTask`
2. 业务规则：阶段状态机、审批校验、审计留痕
3. 决策逻辑：按状态选择审批路径与回退策略

## 4. 输出

- `docs/hivemind-suite-map.md`
- `docs/scene.yaml`
- `docs/scene-package.json`

