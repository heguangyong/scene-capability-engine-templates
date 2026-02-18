---
name: multi-module-navigation
category: other
description: Template for Multi Module Navigation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实现计划：多模块导航集成

## 概述

在 5 个现有文件中追加配置项，将 Order、Facility、Invoice、Shipment 4 个模块集成到前端导航系统。遵循已有的 Party/Product 导航模式。

## 任务

- [x] 1. 图标注册与导航项配置
  - [x] 1.1 在 `frontend/src/renderer/utils/icons.js` 中导入 ShoppingCart、Warehouse、Receipt、Truck 图标，并注册到 icons 导出对象
    - 从 lucide-vue-next 导入 4 个图标组件
    - 在 icons 对象中添加 shoppingCart、warehouse、receipt、truck 映射
    - _Requirements: 1.1, 1.2_
  - [x] 1.2 在 `frontend/src/renderer/App.vue` 的 navItems 数组中追加 4 个导航项
    - order（ShoppingCart, /order, 订单管理）
    - facility（Warehouse, /facility, 设施管理）
    - invoice（Receipt, /invoice, 发票管理）
    - shipment（Truck, /shipment, 发货管理）
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_
  - [x] 1.3 在 App.vue 的 route watcher 和 onMounted 中追加 4 个路径匹配
    - /order → order, /facility → facility, /invoice → invoice, /shipment → shipment
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

- [x] 2. 导航状态 Store 扩展
  - [x] 2.1 在 `frontend/src/renderer/stores/navigation.ts` 中扩展 NavId 类型、PanelContext、DEFAULT_CONTEXTS 和 componentMap
    - NavId 追加 'order' | 'facility' | 'invoice' | 'shipment'
    - PanelContext 追加 4 个空记录类型
    - DEFAULT_CONTEXTS 追加 4 个空对象
    - componentMap 追加 4 个面板映射
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [x] 3. 面板组件注册
  - [x] 3.1 在 `frontend/src/renderer/components/panels/ContextPanel.vue` 中注册 4 个异步面板组件
    - defineAsyncComponent 加载 OrderPanel、FacilityPanel、InvoicePanel、ShipmentPanel
    - panelComponentMap 追加 4 个映射
    - _Requirements: 4.1, 4.2_

- [x] 4. 路由配置
  - [x] 4.1 在 `frontend/src/renderer/router/index.js` 中追加 8 条路由
    - /order + /order/:orderId（懒加载 OrderListView、OrderDetailView）
    - /facility + /facility/:facilityId（懒加载 FacilityListView、FacilityDetailView）
    - /invoice + /invoice/:invoiceId（懒加载 InvoiceListView、InvoiceDetailView）
    - /shipment + /shipment/:shipmentId（懒加载 ShipmentListView、ShipmentDetailView）
    - 所有路由设置 meta: { requiresAuth: true }
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

- [x] 5. 编译验证检查点
  - 从 frontend/ 目录执行 `npm run build`，确认前端编译通过
  - 从 backend/ 目录执行 `mvn clean package`，确认后端编译通过（验证 Codex agent 产物无 BOM 等问题）
  - 如有编译错误，修复后重新验证
  - _Requirements: 7.1, 7.2_

## 备注

- 所有修改遵循已有的 Party/Product 导航模式，保持代码一致性
- 4 个模块的 views/panels/stores/api 已在 Spec 32-00 ~ 35-00 中实现
- 编译验证是本 Spec 的核心质量保证手段
