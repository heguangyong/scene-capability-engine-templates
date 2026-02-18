---
name: facility-inventory
category: other
description: Template for Facility Inventory
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 库存设施管理模块 (Facility & Inventory)

## 概述
实现 Moqui ERP 设施与库存管理模块的 REST API 后端和 Vue 3 前端，支持设施 CRUD 和库存资产查询。

---

## 用户故事与验收标准

### 1. 设施列表查询
**作为** ERP 操作员，**我想** 查看设施列表并支持分页和搜索，**以便** 快速找到目标设施。

**验收标准:**
- 1.1 GET `/api/v1/facility` 返回分页设施列表（pageIndex, pageSize, totalCount, totalPages）
- 1.2 支持按 facilityTypeEnumId 筛选
- 1.3 支持按 searchKeyword 模糊搜索设施名称（facilityName）
- 1.4 每条设施返回: facilityId, facilityName, facilityTypeEnumId, ownerPartyId, description
- 1.5 前端 FacilityListView 展示设施表格，支持分页、搜索、类型筛选

### 2. 设施详情查看
**作为** ERP 操作员，**我想** 查看设施的完整详情，**以便** 了解设施信息和库存。

**验收标准:**
- 2.1 GET `/api/v1/facility/{facilityId}` 返回设施详情 + 库存资产列表
- 2.2 设施详情包含: facilityId, facilityName, facilityTypeEnumId, ownerPartyId, description, geoPointId
- 2.3 库存资产列表: assetId, productId, productName, quantityOnHandTotal, availableToPromiseTotal, statusId
- 2.4 前端 FacilityDetailView 展示设施信息和库存资产表格

### 3. 创建设施
**作为** ERP 操作员，**我想** 创建新设施，**以便** 管理仓库和存储位置。

**验收标准:**
- 3.1 POST `/api/v1/facility` 创建设施，必填: facilityName, facilityTypeEnumId
- 3.2 返回新创建的 facilityId
- 3.3 前端 FacilityFormDialog 提供创建表单

### 4. 更新设施
**作为** ERP 操作员，**我想** 修改设施信息，**以便** 更正或补充设施数据。

**验收标准:**
- 4.1 PUT `/api/v1/facility/{facilityId}` 更新设施字段
- 4.2 仅更新请求中包含的字段
- 4.3 设施不存在时返回 404

### 5. 库存资产查询
**作为** ERP 操作员，**我想** 查看某设施的库存资产，**以便** 了解库存状况。

**验收标准:**
- 5.1 GET `/api/v1/facility/{facilityId}/assets` 返回该设施的库存资产列表
- 5.2 支持按 productId 筛选
- 5.3 支持按 statusId 筛选（AstAvailable, AstOnHold 等）
- 5.4 返回: assetId, productId, quantityOnHandTotal, availableToPromiseTotal, statusId, receivedDate

### 6. 设施类型列表
**作为** ERP 操作员，**我想** 获取设施类型枚举，**以便** 在创建设施时选择类型。

**验收标准:**
- 6.1 GET `/api/v1/facility/types` 返回设施类型枚举列表
- 6.2 从 Enumeration 表查询 enumTypeId=FacilityType 的记录

---

## 非功能需求
- 遵循现有 PartyService/ProductService 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式

## Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 设施 | `mantle.facility.Facility` |
| 库存资产 | `mantle.product.asset.Asset` |
| 设施类型 | `moqui.basic.Enumeration` (FacilityType) |
