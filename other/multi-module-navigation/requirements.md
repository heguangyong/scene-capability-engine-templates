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

# 需求文档

## 简介

统一集成 4 个业务模块（Order 订单、Facility 设施、Invoice 发票、Shipment 发货）的前端导航。这些模块的后端 Service/Controller 和前端 views/panels/stores 已全部实现，唯一缺失的是前端导航入口的注册与路由配置。本 Spec 是一个轻量集成任务，遵循已有的 Party/Product 导航模式。

## 术语表

- **Navigation_System**: 前端应用的导航系统，包括侧边栏图标、路由配置、面板映射和导航状态管理
- **NavItem**: 侧边栏导航项，包含 id、route、label、icon 等属性
- **ContextPanel**: 中间面板组件，根据当前激活的导航 ID 动态渲染对应的业务面板
- **NavId**: 导航标识类型，用于标识当前激活的导航模块
- **Icon_Registry**: 图标注册表（icons.js），统一管理应用中使用的所有 lucide-vue-next 图标

## 需求

### 需求 1：图标注册

**用户故事：** 作为开发者，我希望在图标注册表中导入 4 个模块所需的图标，以便导航栏能正确显示模块图标。

#### 验收标准

1. THE Icon_Registry SHALL 从 lucide-vue-next 导入 ShoppingCart、Warehouse、Receipt、Truck 四个图标组件
2. THE Icon_Registry SHALL 在 icons 导出对象中注册 shoppingCart、warehouse、receipt、truck 四个图标映射

### 需求 2：导航项配置

**用户故事：** 作为用户，我希望在侧边栏看到订单管理、设施管理、发票管理、发货管理四个导航入口，以便快速访问各业务模块。

#### 验收标准

1. WHEN Navigation_System 初始化时，THE Navigation_System SHALL 在 navItems 数组中包含 4 个新导航项：order（订单管理）、facility（设施管理）、invoice（发票管理）、shipment（发货管理）
2. THE Navigation_System SHALL 为 order 导航项使用 ShoppingCart 图标、路由 /order、标签"订单管理"
3. THE Navigation_System SHALL 为 facility 导航项使用 Warehouse 图标、路由 /facility、标签"设施管理"
4. THE Navigation_System SHALL 为 invoice 导航项使用 Receipt 图标、路由 /invoice、标签"发票管理"
5. THE Navigation_System SHALL 为 shipment 导航项使用 Truck 图标、路由 /shipment、标签"发货管理"

### 需求 3：导航状态管理

**用户故事：** 作为开发者，我希望导航状态 store 支持 4 个新模块的 NavId 类型和面板上下文，以便导航切换时状态正确管理。

#### 验收标准

1. THE Navigation_System SHALL 在 NavId 类型中包含 'order'、'facility'、'invoice'、'shipment' 四个值
2. THE Navigation_System SHALL 在 PanelContext 类型中为 order、facility、invoice、shipment 定义空记录类型
3. THE Navigation_System SHALL 在 DEFAULT_CONTEXTS 中为 order、facility、invoice、shipment 提供空对象默认值
4. THE Navigation_System SHALL 在 currentPanelComponent getter 的 componentMap 中映射 order→OrderPanel、facility→FacilityPanel、invoice→InvoicePanel、shipment→ShipmentPanel

### 需求 4：面板组件注册

**用户故事：** 作为用户，我希望点击导航项后中间面板能正确显示对应模块的面板内容。

#### 验收标准

1. THE ContextPanel SHALL 使用 defineAsyncComponent 异步加载 OrderPanel、FacilityPanel、InvoicePanel、ShipmentPanel 四个面板组件
2. THE ContextPanel SHALL 在 panelComponentMap 中注册 order→OrderPanel、facility→FacilityPanel、invoice→InvoicePanel、shipment→ShipmentPanel 的映射

### 需求 5：路由配置

**用户故事：** 作为用户，我希望能通过 URL 直接访问各模块的列表页和详情页。

#### 验收标准

1. THE Navigation_System SHALL 为 order 模块配置 /order（列表）和 /order/:orderId（详情）两条路由
2. THE Navigation_System SHALL 为 facility 模块配置 /facility（列表）和 /facility/:facilityId（详情）两条路由
3. THE Navigation_System SHALL 为 invoice 模块配置 /invoice（列表）和 /invoice/:invoiceId（详情）两条路由
4. THE Navigation_System SHALL 为 shipment 模块配置 /shipment（列表）和 /shipment/:shipmentId（详情）两条路由
5. THE Navigation_System SHALL 为所有新路由设置 requiresAuth: true 元数据

### 需求 6：路由同步

**用户故事：** 作为用户，我希望通过 URL 直接访问模块页面时，侧边栏能正确高亮对应的导航项。

#### 验收标准

1. WHEN 路由路径以 /order 开头时，THE Navigation_System SHALL 将 activeNavId 设置为 'order'
2. WHEN 路由路径以 /facility 开头时，THE Navigation_System SHALL 将 activeNavId 设置为 'facility'
3. WHEN 路由路径以 /invoice 开头时，THE Navigation_System SHALL 将 activeNavId 设置为 'invoice'
4. WHEN 路由路径以 /shipment 开头时，THE Navigation_System SHALL 将 activeNavId 设置为 'shipment'

### 需求 7：编译验证

**用户故事：** 作为开发者，我希望集成后前后端编译均通过，确保不引入编译错误。

#### 验收标准

1. WHEN 执行 mvn clean package 时，THE 后端项目 SHALL 编译成功且无错误
2. WHEN 执行 npm run build 时，THE 前端项目 SHALL 编译成功且无错误
