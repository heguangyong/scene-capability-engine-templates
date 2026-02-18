---
name: shipment-management
category: other
description: Template for Shipment Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 发货管理模块 — 设计文档

## 1. 架构概览
遵循项目现有模式：Service → Controller → Router 注册（后端），Types → ApiService → Store → Views（前端）。

## 2. 后端设计

### 2.1 ShipmentService.java
- 包路径: `org.moqui.rest.service.ShipmentService`
- 构造函数注入 `ExecutionContextFactory`
- 方法:
  - `listShipments(pageIndex, pageSize, shipmentTypeEnumId, statusId, searchKeyword)` → 分页查询 `mantle.shipment.Shipment`
  - `getShipmentDetail(shipmentId)` → 查询发货单头 + ShipmentItem + ShipmentPackage
  - `createShipment(data)` → 创建 Shipment，初始状态 ShipInput
  - `updateShipment(shipmentId, data)` → 更新 Shipment 字段
  - `updateStatus(shipmentId, statusId)` → 更新发货单状态
  - `addItem(shipmentId, data)` → 创建 ShipmentItem
  - `updateItem(shipmentId, shipmentItemSeqId, data)` → 更新 ShipmentItem
  - `deleteItem(shipmentId, shipmentItemSeqId)` → 删除 ShipmentItem
  - `addPackage(shipmentId, data)` → 创建 ShipmentPackage
  - `updatePackage(shipmentId, shipmentPackageSeqId, data)` → 更新 ShipmentPackage
  - `deletePackage(shipmentId, shipmentPackageSeqId)` → 删除 ShipmentPackage
- 错误处理: 使用 `errorResult(errorCode, errorMessage)` 模式

### 2.2 ShipmentController.java
- 包路径: `org.moqui.rest.controller.ShipmentController`
- 路由注册:
  ```
  GET    /api/v1/shipment                                                       → listShipments
  GET    /api/v1/shipment/{shipmentId}                                          → getShipmentDetail
  POST   /api/v1/shipment                                                       → createShipment
  PUT    /api/v1/shipment/{shipmentId}                                          → updateShipment
  POST   /api/v1/shipment/{shipmentId}/update-status                            → updateStatus
  POST   /api/v1/shipment/{shipmentId}/items                                    → addItem
  PUT    /api/v1/shipment/{shipmentId}/items/{shipmentItemSeqId}                → updateItem
  DELETE /api/v1/shipment/{shipmentId}/items/{shipmentItemSeqId}                → deleteItem
  POST   /api/v1/shipment/{shipmentId}/packages                                 → addPackage
  PUT    /api/v1/shipment/{shipmentId}/packages/{shipmentPackageSeqId}          → updatePackage
  DELETE /api/v1/shipment/{shipmentId}/packages/{shipmentPackageSeqId}          → deletePackage
  ```

### 2.3 RestApplication.java 注册
```java
ShipmentService shipmentService = new ShipmentService(ecf);
new ShipmentController(shipmentService).register(router);
```

## 3. 前端设计

### 3.1 类型定义 — `types/shipment.ts`
- ShipmentListItem, ShipmentDetail, ShipmentItemEntry, ShipmentPackageEntry
- CreateShipmentRequest, UpdateShipmentRequest, ShipmentItemRequest, ShipmentPackageRequest
- ShipmentListParams, ShipmentPaginationMeta

### 3.2 API 服务 — `services/shipmentApiService.ts`
- 使用 `apiV1Client`，路径前缀: `/shipment`

### 3.3 Pinia Store — `stores/shipment.ts`
- 状态: shipments, pagination, loading, error, currentShipment, detailLoading, mutating
- Actions: fetchShipments, searchShipments, fetchShipmentDetail, createShipment, updateShipment, updateStatus, addItem/updateItem/deleteItem, addPackage/updatePackage/deletePackage

### 3.4 视图组件
- `ShipmentListView.vue` — 发货单列表页
- `ShipmentDetailView.vue` — 发货单详情页
- `ShipmentFormDialog.vue` — 创建/编辑发货单对话框

### 3.5 ContextPanel — `components/panels/ShipmentPanel.vue`

## 4. Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 发货单头 | `mantle.shipment.Shipment` |
| 发货项 | `mantle.shipment.ShipmentItem` |
| 发货包裹 | `mantle.shipment.ShipmentPackage` |

## 5. 测试策略
- 后端: JUnit 5 + jqwik 属性测试
- 前端: Vitest 单元测试

## 6. 导航集成（⚠️ 暂不执行，由 Kiro 统一处理）
- App.vue navItems 添加 shipment 项（icon: Truck）
- navigation.ts 添加 shipment NavId
- ContextPanel 注册 ShipmentPanel
- router/index.js 添加 /shipment, /shipment/:shipmentId 路由
- icons.js 导入 Truck 图标
