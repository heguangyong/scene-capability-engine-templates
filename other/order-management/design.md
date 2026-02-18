---
name: order-management
category: other
description: Template for Order Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 订单管理模块 — 设计文档

## 1. 架构概览
遵循项目现有模式：Service → Controller → Router 注册（后端），Types → ApiService → Store → Views（前端）。

## 2. 后端设计

### 2.1 OrderService.java
- 包路径: `org.moqui.rest.service.OrderService`
- 构造函数注入 `ExecutionContextFactory`
- 方法:
  - `listOrders(pageIndex, pageSize, statusId, searchKeyword)` → 分页查询 `mantle.order.OrderHeader`
  - `getOrderDetail(orderId)` → 查询订单头 + OrderItem + OrderPart parties
  - `createOrder(data)` → 创建 OrderHeader，初始状态 OrderOpen，自动设置 entryDate
  - `updateOrder(orderId, data)` → 更新 OrderHeader 字段
  - `updateStatus(orderId, statusId)` → 更新订单状态
  - `addItem(orderId, data)` → 创建 OrderItem，自动生成 orderItemSeqId
  - `updateItem(orderId, orderItemSeqId, data)` → 更新 OrderItem
  - `deleteItem(orderId, orderItemSeqId)` → 删除 OrderItem
  - `addParty(orderId, data)` → 创建 OrderPartParty
  - `removeParty(orderId, partyId, roleTypeId)` → 删除 OrderPartParty
- 错误处理: 使用 `errorResult(errorCode, errorMessage)` 模式（同 ProductService）

### 2.2 OrderController.java
- 包路径: `org.moqui.rest.controller.OrderController`
- 路由注册:
  ```
  GET    /api/v1/order                                              → listOrders
  GET    /api/v1/order/{orderId}                                    → getOrderDetail
  POST   /api/v1/order                                              → createOrder
  PUT    /api/v1/order/{orderId}                                    → updateOrder
  POST   /api/v1/order/{orderId}/update-status                      → updateStatus
  POST   /api/v1/order/{orderId}/items                              → addItem
  PUT    /api/v1/order/{orderId}/items/{orderItemSeqId}             → updateItem
  DELETE /api/v1/order/{orderId}/items/{orderItemSeqId}             → deleteItem
  POST   /api/v1/order/{orderId}/parties                            → addParty
  DELETE /api/v1/order/{orderId}/parties/{partyId}/{roleTypeId}     → removeParty
  ```

### 2.3 RestApplication.java 注册
```java
OrderService orderService = new OrderService(ecf);
new OrderController(orderService).register(router);
```

## 3. 前端设计

### 3.1 类型定义 — `types/order.ts`
- OrderListItem, OrderDetail, OrderItemEntry, OrderPartyEntry
- CreateOrderRequest, UpdateOrderRequest, OrderItemRequest
- OrderListParams, OrderPaginationMeta

### 3.2 API 服务 — `services/orderApiService.ts`
- 使用 `apiV1Client` 调用后端 API，路径前缀: `/order`

### 3.3 Pinia Store — `stores/order.ts`
- 状态: orders, pagination, loading, error, currentOrder, detailLoading, mutating
- Actions: fetchOrders, searchOrders, setPage, fetchOrderDetail, createOrder, updateOrder, updateStatus, addItem, updateItem, deleteItem, addParty, removeParty

### 3.4 视图组件
- `OrderListView.vue` — 订单列表页
- `OrderDetailView.vue` — 订单详情页
- `OrderFormDialog.vue` — 创建/编辑订单对话框

### 3.5 ContextPanel — `components/panels/OrderPanel.vue`

## 4. Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 订单头 | `mantle.order.OrderHeader` |
| 订单项 | `mantle.order.OrderItem` |
| 订单分部 | `mantle.order.OrderPart` |
| 订单方 | `mantle.order.OrderPartParty` |

## 5. 测试策略
- 后端: JUnit 5 + jqwik 属性测试
- 前端: Vitest 单元测试

## 6. 导航集成（⚠️ 暂不执行，由 Kiro 统一处理）
- App.vue navItems 添加 order 项（icon: ShoppingCart）
- navigation.ts 添加 order NavId + panelContext
- ContextPanel 注册 OrderPanel
- router/index.js 添加 /order, /order/:orderId 路由
- icons.js 导入 ShoppingCart 图标
