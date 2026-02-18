---
name: moqui-scene-composition-visualization-program
category: other
description: Template for Moqui Scene Composition Visualization Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 场景编排可视化计划（68-00）

## 设计目标

- 以 SuiteHub 为统一入口，展示三套件场景结构；
- 直接透出 ontology 三层，让页面本身成为“场景说明书”；
- 保持前端最小改动，不影响既有业务流程页面。

## 子 Spec 拆分

- `68-01-suite-scene-canvas-visualization`
- `68-02-scene-composition-interaction-hardening`
- `68-03-scene-visualization-governance-closure`

## 方案

1. 从 `scene-blueprints` 提取模块级场景信息；  
2. 在 `SuiteHub` 新增“场景组合画布”区；  
3. 增加套件过滤与依赖提示；  
4. 完成治理与模板收口。  

## 验证

- `npm run build`（frontend）
- `npx kse status --verbose`
- `npx kse doctor --docs`
- `scripts/kse-final-sync.ps1`（70/100 ontology gate）

