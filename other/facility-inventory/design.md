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

# 库存设施管理模块 — 设计文档

## 1. 架构概览
遵循项目现有模式：Service → Controller → Router 注册（后端），Types → ApiService → Store → Views（前端）。

## 2. 后端设计

### 2.1 FacilityService.java
- 包路径: `org.moqui.rest.service.FacilityService`
- 构造函数注入 `ExecutionContextFactory`
- 方法:
  - `listFacilities(pageIndex, pageSize, facilityTypeEnumId, searchKeyword)` → 分页查询 `mantle.facility.Facility`
  - `getFacilityDetail(facilityId)` → 查询设施详情 + 库存资产列表
  - `createFacility(data)` → 创建 Facility
  - `updateFacility(facilityId, data)` → 更新 Facility
  - `listAssets(facilityId, productId, statusId)` → 查询设施库存资产
  - `listFacilityTypes()` → 查询设施类型枚举
- 错误处理: 使用 `errorResult(errorCode, errorMessage)` 模式

### 2.2 FacilityController.java
- 包路径: `org.moqui.rest.controller.FacilityController`
- 路由注册:
  ```
  GET    /api/v1/facility                          → listFacilities
  GET    /api/v1/facility/types                    → listFacilityTypes
  GET    /api/v1/facility/{facilityId}             → getFacilityDetail
  POST   /api/v1/facility                          → createFacility
  PUT    /api/v1/facility/{facilityId}             → updateFacility
  GET    /api/v1/facility/{facilityId}/assets      → listAssets
  ```

### 2.3 RestApplication.java 注册
```java
FacilityService facilityService = new FacilityService(ecf);
new FacilityController(facilityService).register(router);
```

## 3. 前端设计

### 3.1 类型定义 — `types/facility.ts`
- FacilityListItem, FacilityDetail, AssetItem, FacilityType
- CreateFacilityRequest, UpdateFacilityRequest
- FacilityListParams, FacilityPaginationMeta

### 3.2 API 服务 — `services/facilityApiService.ts`
- 使用 `apiV1Client`，路径前缀: `/facility`

### 3.3 Pinia Store — `stores/facility.ts`
- 状态: facilities, pagination, loading, error, currentFacility, detailLoading, facilityTypes, mutating
- Actions: fetchFacilities, searchFacilities, setPage, fetchFacilityDetail, createFacility, updateFacility, fetchFacilityTypes

### 3.4 视图组件
- `FacilityListView.vue` — 设施列表页
- `FacilityDetailView.vue` — 设施详情页（含库存资产表格）
- `FacilityFormDialog.vue` — 创建/编辑设施对话框

### 3.5 ContextPanel — `components/panels/FacilityPanel.vue`

## 4. Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 设施 | `mantle.facility.Facility` |
| 库存资产 | `mantle.product.asset.Asset` |
| 设施类型 | `moqui.basic.Enumeration` (FacilityType) |

## 5. 测试策略
- 后端: JUnit 5 + jqwik 属性测试
- 前端: Vitest 单元测试

## 6. 导航集成（⚠️ 暂不执行，由 Kiro 统一处理）
- App.vue navItems 添加 facility 项（icon: Warehouse）
- navigation.ts 添加 facility NavId
- ContextPanel 注册 FacilityPanel
- router/index.js 添加 /facility, /facility/:facilityId 路由
- icons.js 导入 Warehouse 图标
