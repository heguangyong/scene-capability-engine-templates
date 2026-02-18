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

# 订单管理模块 ({{SPEC_NAME_TITLE}})

## 概述
实现 Moqui ERP 订单管理模块的 REST API 后端和 Vue 3 前端，支持订单的完整生命周期管理。

---

## 用户故事与验收标准

### 1. 订单列表查询
**作为** ERP 操作员，**我想** 查看订单列表并支持分页、搜索和筛选，**以便** 快速找到目标订单。

**验收标准:**
- 1.1 GET `/api/v1/order` 返回分页订单列表（pageIndex, pageSize, totalCount, totalPages）
- 1.2 支持按 statusId 筛选订单
- 1.3 支持按 searchKeyword 模糊搜索订单名称（orderName）
- 1.4 每条订单返回: orderId, orderName, statusId, currencyUomId, grandTotal, placedDate, entryDate
- 1.5 前端 OrderListView 展示订单表格，支持分页、搜索、状态筛选

### 2. 订单详情查看
**作为** ERP 操作员，**我想** 查看订单的完整详情，**以便** 了解订单的所有信息。

**验收标准:**
- 2.1 GET `/api/v1/order/{orderId}` 返回订单头信息 + 订单项列表 + 订单方列表
- 2.2 订单项包含: orderItemSeqId, orderPartSeqId, productId, itemDescription, quantity, unitAmount, itemTypeEnumId
- 2.3 订单方包含: partyId, roleTypeId, displayName（通过 Party 查找）
- 2.4 前端 OrderDetailView 展示订单头、订单项表格、订单方列表

### 3. 创建订单
**作为** ERP 操作员，**我想** 创建新订单，**以便** 记录客户的采购需求。

**验收标准:**
- 3.1 POST `/api/v1/order` 创建订单头，必填: orderName, currencyUomId
- 3.2 初始状态设为 OrderOpen
- 3.3 自动设置 entryDate 为当前时间
- 3.4 返回新创建的 orderId
- 3.5 前端 OrderFormDialog 提供创建表单

### 4. 更新订单
**作为** ERP 操作员，**我想** 修改订单基本信息，**以便** 更正或补充订单数据。

**验收标准:**
- 4.1 PUT `/api/v1/order/{orderId}` 更新订单头字段（orderName, currencyUomId 等）
- 4.2 仅更新请求中包含的字段
- 4.3 订单不存在时返回 404

### 5. 订单状态管理
**作为** ERP 操作员，**我想** 推进订单状态，**以便** 管理订单生命周期。

**验收标准:**
- 5.1 POST `/api/v1/order/{orderId}/update-status` 更新订单状态
- 5.2 请求体包含 statusId（目标状态）
- 5.3 支持的状态: OrderOpen, OrderPlaced, OrderApproved, OrderCompleted, OrderCancelled
- 5.4 前端提供状态操作按钮

### 6. 订单项管理
**作为** ERP 操作员，**我想** 管理订单中的商品项，**以便** 维护订单内容。

**验收标准:**
- 6.1 POST `/api/v1/order/{orderId}/items` 添加订单项，必填: productId, quantity, unitAmount
- 6.2 PUT `/api/v1/order/{orderId}/items/{orderItemSeqId}` 更新订单项
- 6.3 DELETE `/api/v1/order/{orderId}/items/{orderItemSeqId}` 删除订单项
- 6.4 自动生成 orderItemSeqId（递增序列）

### 7. 订单方管理
**作为** ERP 操作员，**我想** 管理订单关联方，**以便** 记录客户、供应商等角色。

**验收标准:**
- 7.1 POST `/api/v1/order/{orderId}/parties` 添加订单方，必填: partyId, roleTypeId
- 7.2 DELETE `/api/v1/order/{orderId}/parties/{partyId}/{roleTypeId}` 移除订单方
- 7.3 支持的角色: Customer, Vendor, BillToCustomer 等

---

## 非功能需求
- 遵循现有 PartyService/ProductService 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式
