---
name: scene-composition-interaction-hardening
category: other
description: Template for Scene Composition Interaction Hardening
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：场景编排交互加固（68-02）

## 页面范围

- `frontend/src/renderer/views/SuiteHubView.vue`

## 关键设计

1. 增加套件过滤器（all/hivemind/popcommerce/marbleerp）；  
2. 依赖提示来源：`sceneBlueprint.relatedModules`；  
3. 依赖按钮直接路由跳转。  

## 验证

- `npm run build`（frontend）
- `/suite-hub` 手动验证过滤与依赖跳转

