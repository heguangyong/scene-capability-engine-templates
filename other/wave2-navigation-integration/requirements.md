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

# 需求文档：Wave 2 多模块导航集成

## 概述

将 Wave 2 的 4 个业务模块（BOM 管理、工艺路线、采购管理、库存管理）集成到前端导航系统中。遵循 Spec 36-00（Wave 1 导航集成）的模式，在现有配置文件中追加条目。

## 背景

Wave 2 的 4 个模块（Spec 37-00 ~ 40-00）已完成开发，包括后端 Service/Controller、前端 types/api/store/views/panel。但这些模块尚未注册到前端导航系统中，用户无法通过侧边栏访问。

## 验收标准

### 1. 图标注册

- 1.1 从 lucide-vue-next 导入 ClipboardList 图标（采购管理用）
- 1.2 在 icons 导出对象中注册 clipboardList 映射
- 1.3 BOM 使用 layers 图标、工艺路线使用 workflow 图标、采购使用 clipboardList 图标、库存使用 box 图标

### 2. 侧边栏导航项

- 2.1 navItems 追加 BOM 管理项（id: bom, route: /bom, icon: layers, label: BOM 管理）
- 2.2 navItems 追加工艺路线项（id: routing, route: /routing, icon: workflow, label: 工艺路线）
- 2.3 navItems 追加采购管理项（id: procurement, route: /procurement, icon: clipboardList, label: 采购管理）
- 2.4 navItems 追加库存管理项（id: inventory, route: /inventory, icon: box, label: 库存管理）
- 2.5 4 个导航项均包含中文 label 和 description

### 3. 导航状态 Store

- 3.1 NavId 类型追加 'bom' | 'routing' | 'procurement' | 'inventory'
- 3.2 PanelContext 追加 4 个空记录类型
- 3.3 DEFAULT_CONTEXTS 追加 4 个空对象
- 3.4 componentMap 追加 4 个面板映射（BomPanel、RoutingPanel、ProcurementPanel、InventoryPanel）

### 4. 面板组件注册

- 4.1 ContextPanel.vue 中用 defineAsyncComponent 加载 4 个面板组件
- 4.2 panelComponentMap 追加 4 个映射

### 5. 路由配置

- 5.1 追加 /bom 和 /bom/:bomId 路由（懒加载 BomListView、BomDetailView）
- 5.2 追加 /routing 和 /routing/:routingId 路由（懒加载 RoutingListView、RoutingDetailView）
- 5.3 追加 /procurement 和 /procurement/:procurementId 路由（懒加载 ProcurementListView、ProcurementDetailView）
- 5.4 追加 /inventory 和 /inventory/:inventoryId 路由（懒加载 InventoryListView、InventoryDetailView）
- 5.5 所有 8 条路由设置 meta: { requiresAuth: true }

### 6. 路由状态同步

- 6.1 路由变化到 /bom 时，activeNavId 设为 'bom'
- 6.2 路由变化到 /routing 时，activeNavId 设为 'routing'
- 6.3 路由变化到 /procurement 时，activeNavId 设为 'procurement'
- 6.4 路由变化到 /inventory 时，activeNavId 设为 'inventory'

### 7. 编译验证

- 7.1 前端 `npm run build` 编译通过
- 7.2 后端 `mvn clean package` 编译通过（验证 Codex agent 产物无 BOM 编码问题）
