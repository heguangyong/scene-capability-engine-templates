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

# 任务清单：Moqui 三大应用能力闭环（64-00）

- [x] 1 建立三大应用映射基线
  - [x] 1.1 汇总 HiveMind / PopCommerce / MarbleERP 原始能力画像
  - [x] 1.2 映射到当前 331 菜单模块、路由与关联 Spec
  - [x] 1.3 输出三层（实体关系/业务规则/决策逻辑）映射文档

- [x] 2 生成 ontology 场景合同
  - [x] 2.1 新增 `docs/scene.yaml`
  - [x] 2.2 新增 `docs/scene-package.json`
  - [x] 2.3 完成 scene 严格校验

- [x] 3 回填治理资产
  - [x] 3.1 更新 `.kiro/steering/CURRENT_CONTEXT.md`
  - [x] 3.2 更新 `docs/handoffs/handoff-manifest.json`
  - [x] 3.3 更新 handoff 证据索引（ontology/template）

- [x] 4 运行总体验证
  - [x] 4.1 执行 `npx kse status --verbose`
  - [x] 4.2 执行 `npx kse doctor --docs`
  - [x] 4.3 执行 `npx kse templates create-from-spec --spec {{SPEC_NAME}} --no-interactive`

