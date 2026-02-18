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

# 发货管理模块 ({{SPEC_NAME_TITLE}})

## 概述
实现 Moqui ERP 发货管理模块的 REST API 后端和 Vue 3 前端，支持发货单的完整生命周期管理。

---

## 用户故事与验收标准

### 1. 发货单列表查询
**作为** ERP 操作员，**我想** 查看发货单列表并支持分页和筛选，**以便** 快速找到目标发货单。

**验收标准:**
- 1.1 GET `/api/v1/shipment` 返回分页发货单列表（pageIndex, pageSize, totalCount, totalPages）
- 1.2 支持按 shipmentTypeEnumId 筛选
- 1.3 支持按 statusId 筛选
- 1.4 支持按 searchKeyword 模糊搜索（shipmentId 或 handlingInstructions）
- 1.5 每条发货单返回: shipmentId, shipmentTypeEnumId, statusId, fromPartyId, toPartyId, estimatedShipDate, estimatedArrivalDate
- 1.6 前端 ShipmentListView 展示发货单表格

### 2. 发货单详情查看
**作为** ERP 操作员，**我想** 查看发货单的完整详情。

**验收标准:**
- 2.1 GET `/api/v1/shipment/{shipmentId}` 返回发货单头 + 发货项列表 + 发货包裹列表
- 2.2 发货项包含: shipmentItemSeqId, productId, quantity
- 2.3 发货包裹包含: shipmentPackageSeqId, weight, weightUomId, trackingCode
- 2.4 前端 ShipmentDetailView 展示发货单头、发货项表格、包裹列表

### 3. 创建发货单
**作为** ERP 操作员，**我想** 创建新发货单。

**验收标准:**
- 3.1 POST `/api/v1/shipment` 创建发货单，必填: shipmentTypeEnumId, fromPartyId, toPartyId
- 3.2 初始状态设为 ShipInput
- 3.3 返回新创建的 shipmentId
- 3.4 前端 ShipmentFormDialog 提供创建表单

### 4. 更新发货单
**作为** ERP 操作员，**我想** 修改发货单基本信息。

**验收标准:**
- 4.1 PUT `/api/v1/shipment/{shipmentId}` 更新发货单头字段
- 4.2 仅更新请求中包含的字段
- 4.3 发货单不存在时返回 404

### 5. 发货单状态管理
**作为** ERP 操作员，**我想** 推进发货单状态。

**验收标准:**
- 5.1 POST `/api/v1/shipment/{shipmentId}/update-status` 更新发货单状态
- 5.2 支持的状态: ShipInput, ShipScheduled, ShipPicked, ShipPacked, ShipShipped, ShipDelivered, ShipCancelled

### 6. 发货项管理
**作为** ERP 操作员，**我想** 管理发货单中的商品项。

**验收标准:**
- 6.1 POST `/api/v1/shipment/{shipmentId}/items` 添加发货项，必填: productId, quantity
- 6.2 PUT `/api/v1/shipment/{shipmentId}/items/{shipmentItemSeqId}` 更新发货项
- 6.3 DELETE `/api/v1/shipment/{shipmentId}/items/{shipmentItemSeqId}` 删除发货项

### 7. 发货包裹管理
**作为** ERP 操作员，**我想** 管理发货包裹信息。

**验收标准:**
- 7.1 POST `/api/v1/shipment/{shipmentId}/packages` 添加包裹
- 7.2 PUT `/api/v1/shipment/{shipmentId}/packages/{shipmentPackageSeqId}` 更新包裹
- 7.3 DELETE `/api/v1/shipment/{shipmentId}/packages/{shipmentPackageSeqId}` 删除包裹

---

## 非功能需求
- 遵循现有 PartyService/ProductService 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构

## Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 发货单头 | `mantle.shipment.Shipment` |
| 发货项 | `mantle.shipment.ShipmentItem` |
| 发货包裹 | `mantle.shipment.ShipmentPackage` |
| 包裹内容 | `mantle.shipment.ShipmentPackageContent` |
