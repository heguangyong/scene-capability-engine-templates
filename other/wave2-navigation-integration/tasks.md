---
name: wave2-navigation-integration
category: other
description: Template for Wave2 Navigation Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实现计划：Wave 2 多模块导航集成

## 概述

在 5 个现有文件中追加配置项，将 BOM、工艺路线、采购、库存 4 个模块集成到前端导航系统。

## 任务

- [x] 1. 图标注册与导航项配置
  - [x] 1.1 在 `frontend/src/renderer/utils/icons.js` 中导入 ClipboardList 图标并注册到 icons 对象
    - 从 lucide-vue-next 导入 ClipboardList
    - 在 icons 对象中添加 clipboardList: ClipboardList
    - _Requirements: 1.1, 1.2, 1.3_
  - [x] 1.2 在 `frontend/src/renderer/App.vue` 的 navItems 数组中追加 4 个导航项
    - bom（layers, /bom, BOM 管理）
    - routing（workflow, /routing, 工艺路线）
    - procurement（clipboardList, /procurement, 采购管理）
    - inventory（box, /inventory, 库存管理）
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_

- [x] 2. 导航状态 Store 扩展
  - [x] 2.1 在 `frontend/src/renderer/stores/navigation.ts` 中扩展 NavId、PanelContext、DEFAULT_CONTEXTS 和 componentMap
    - NavId 追加 'bom' | 'routing' | 'procurement' | 'inventory'
    - PanelContext 追加 4 个空记录类型
    - DEFAULT_CONTEXTS 追加 4 个空对象
    - componentMap 追加 4 个面板映射
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [x] 3. 面板组件注册
  - [x] 3.1 在 `frontend/src/renderer/components/panels/ContextPanel.vue` 中注册 4 个异步面板组件并追加 panelComponentMap
    - defineAsyncComponent 加载 BomPanel、RoutingPanel、ProcurementPanel、InventoryPanel
    - panelComponentMap 追加 4 个映射
    - _Requirements: 4.1, 4.2_

- [x] 4. 路由配置
  - [x] 4.1 在 `frontend/src/renderer/router/index.js` 中追加 8 条路由
    - /bom + /bom/:bomId
    - /routing + /routing/:routingId
    - /procurement + /procurement/:procurementId
    - /inventory + /inventory/:inventoryId
    - 所有路由 meta: { requiresAuth: true }
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

- [x] 5. 编译验证
  - 从 frontend/ 目录执行 `npm run build`，确认前端编译通过
  - 从 backend/ 目录执行 `mvn clean package`，确认后端编译通过
  - _Requirements: 7.1, 7.2_
