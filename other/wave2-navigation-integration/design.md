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

# 设计文档：Wave 2 多模块导航集成

## 概述

将 4 个 Wave 2 业务模块（BOM、工艺路线、采购、库存）集成到前端导航系统。纯配置性集成，不涉及新组件开发，仅在 5 个现有文件中追加配置项，遵循 Spec 36-00（Wave 1）的导航模式。

## 架构

复用 Wave 1 的三层联动导航架构：

```
侧边栏 App.vue → Navigation Store → ContextPanel + Router
```

集成方式：在每一层的配置数组/映射中追加 4 个模块的条目。

## 修改文件清单（5 个文件）

| 文件 | 修改内容 |
|------|---------|
| `icons.js` | 导入 ClipboardList，注册 clipboardList |
| `App.vue` | navItems 追加 4 项 |
| `navigation.ts` | NavId/PanelContext/DEFAULT_CONTEXTS/componentMap 各追加 4 项 |
| `ContextPanel.vue` | defineAsyncComponent + panelComponentMap 各追加 4 项 |
| `router/index.js` | 追加 8 条路由 |

## 具体配置

### icons.js

新增导入：
```javascript
import { ClipboardList } from 'lucide-vue-next';
```

icons 对象新增：
```javascript
clipboardList: ClipboardList
```

注：layers（BOM）、workflow（路线）、box（库存）已存在于 icons 对象中。

### App.vue navItems 新增

```javascript
{ id: 'bom', route: '/bom', label: 'BOM 管理', icon: icons.layers, description: '管理物料清单' },
{ id: 'routing', route: '/routing', label: '工艺路线', icon: icons.workflow, description: '管理工艺路线' },
{ id: 'procurement', route: '/procurement', label: '采购管理', icon: icons.clipboardList, description: '管理采购订单' },
{ id: 'inventory', route: '/inventory', label: '库存管理', icon: icons.box, description: '管理库存资产' }
```

### navigation.ts

NavId 扩展：
```typescript
export type NavId = '...' | 'bom' | 'routing' | 'procurement' | 'inventory';
```

PanelContext、DEFAULT_CONTEXTS、componentMap 各追加 4 项。

### ContextPanel.vue

```javascript
const BomPanel = defineAsyncComponent(() => import('./BomPanel.vue'));
const RoutingPanel = defineAsyncComponent(() => import('./RoutingPanel.vue'));
const ProcurementPanel = defineAsyncComponent(() => import('./ProcurementPanel.vue'));
const InventoryPanel = defineAsyncComponent(() => import('./InventoryPanel.vue'));
```

### router/index.js 新增 8 条路由

```javascript
{ path: '/bom', name: 'bom-list', component: () => import('../views/BomListView.vue'), meta: { requiresAuth: true } },
{ path: '/bom/:bomId', name: 'bom-detail', component: () => import('../views/BomDetailView.vue'), meta: { requiresAuth: true } },
{ path: '/routing', name: 'routing-list', component: () => import('../views/RoutingListView.vue'), meta: { requiresAuth: true } },
{ path: '/routing/:routingId', name: 'routing-detail', component: () => import('../views/RoutingDetailView.vue'), meta: { requiresAuth: true } },
{ path: '/procurement', name: 'procurement-list', component: () => import('../views/ProcurementListView.vue'), meta: { requiresAuth: true } },
{ path: '/procurement/:procurementId', name: 'procurement-detail', component: () => import('../views/ProcurementDetailView.vue'), meta: { requiresAuth: true } },
{ path: '/inventory', name: 'inventory-list', component: () => import('../views/InventoryListView.vue'), meta: { requiresAuth: true } },
{ path: '/inventory/:inventoryId', name: 'inventory-detail', component: () => import('../views/InventoryDetailView.vue'), meta: { requiresAuth: true } },
```

## 正确性属性

本集成任务为纯配置追加，输入空间固定（4 个模块），通过编译验证 + 配置检查即可保证正确性。

### Property 1：导航配置完整性

*For any* 新增模块 ID（bom、routing、procurement、inventory），icons 导出对象、navItems、panelComponentMap、DEFAULT_CONTEXTS 和 componentMap 均应包含该模块对应条目。

**Validates: Requirements 1.2, 2.1, 3.3, 3.4, 4.2**

### Property 2：路由完整性与认证保护

*For any* 新增模块，路由表应包含 list 和 detail 路由，且 meta.requiresAuth 均为 true。

**Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5**

### Property 3：路由路径与导航状态同步

*For any* 新增模块路径前缀，当路由变化到该路径时，activeNavId 应被设置为对应模块 ID。

**Validates: Requirements 6.1, 6.2, 6.3, 6.4**

## 测试策略

编译验证为核心质量保证手段：
- 前端：`npm run build`
- 后端：`mvn clean package`
