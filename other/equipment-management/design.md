---
name: equipment-management
category: other
description: Template for Equipment Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设备管理 — 设计文档

## 架构概述

设备管理模块遵循项目全栈模式：后端 Service + Controller → 前端 types + store + views + panel → 导航集成。新增 `asset` 子系统。

---

## 后端设计

### Entity 映射

**设备**: 使用 `mantle.product.asset.Asset` entity
- 主键：`assetId`
- 通过 `assetTypeEnumId = 'AstTpEquipment'` 区分设备类型

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| equipmentId | assetId | 主键 |
| equipmentNumber | assetName | 设备编号 EQ-YYYYMMDD-XXXX |
| equipmentName | comments | 设备名称（comments 字段复用） |
| equipmentType | classEnumId | 设备类型 |
| facilityId | facilityId | 所属设施 |
| status | statusId | 设备状态 |
| acquiredDate | acquiredDate | 购入日期 |
| description | serialNumber | 描述（serialNumber 字段复用） |

**维护工单**: 使用 `mantle.work.effort.WorkEffort` entity
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetMaintenanceOrder'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| orderId | workEffortId | 主键 |
| orderNumber | workEffortName | 工单编号 MO-YYYYMMDD-XXXX |
| equipmentId | facilityId | 关联设备（复用 facilityId） |
| maintenanceType | purposeEnumId | 维护类型 |
| priority | priority | 优先级 |
| status | statusId | 工单状态 |
| description | description | 描述 |
| scheduledDate | estimatedStartDate | 计划日期 |
| completedDate | actualCompletionDate | 完成日期 |

### 状态定义

设备状态：
| 状态 ID | 中文 |
|---------|------|
| EqActive | 运行中 |
| EqIdle | 停机 |
| EqMaintenance | 维护中 |
| EqScrapped | 报废 |

维护工单状态：
| 状态 ID | 中文 |
|---------|------|
| MoPending | 待处理 |
| MoInProgress | 进行中 |
| MoCompleted | 已完成 |

### EquipmentService

```java
public class EquipmentService {
    // 设备 CRUD
    public Map<String, Object> getEquipments(int pageIndex, int pageSize, String status);
    public Map<String, Object> getEquipmentById(String equipmentId);
    public Map<String, Object> createEquipment(Map<String, Object> data);
    public Map<String, Object> updateEquipment(String equipmentId, Map<String, Object> data);
    public Map<String, Object> deleteEquipment(String equipmentId);
    public Map<String, Object> changeStatus(String equipmentId, String newStatus);
    
    // 维护工单 CRUD
    public Map<String, Object> getMaintenanceOrders(int pageIndex, int pageSize, String status, String equipmentId);
    public Map<String, Object> getMaintenanceOrderById(String orderId);
    public Map<String, Object> createMaintenanceOrder(Map<String, Object> data);
    public Map<String, Object> updateMaintenanceOrder(String orderId, Map<String, Object> data);
    public Map<String, Object> deleteMaintenanceOrder(String orderId);
    public Map<String, Object> changeOrderStatus(String orderId, String newStatus);
    
    // 编号生成
    public String generateEquipmentNumber(ExecutionContext ec);
    public String generateMaintenanceOrderNumber(ExecutionContext ec);
}
```

### EquipmentController

路由注册：
```
GET    /api/v1/equipments                    → listEquipments
GET    /api/v1/equipments/{id}               → getEquipmentDetail
POST   /api/v1/equipments                    → createEquipment
PUT    /api/v1/equipments/{id}               → updateEquipment
DELETE /api/v1/equipments/{id}               → deleteEquipment
PUT    /api/v1/equipments/{id}/status        → changeStatus
GET    /api/v1/maintenance-orders            → listMaintenanceOrders
GET    /api/v1/maintenance-orders/{id}       → getMaintenanceOrderDetail
POST   /api/v1/maintenance-orders            → createMaintenanceOrder
PUT    /api/v1/maintenance-orders/{id}       → updateMaintenanceOrder
DELETE /api/v1/maintenance-orders/{id}       → deleteMaintenanceOrder
PUT    /api/v1/maintenance-orders/{id}/status → changeOrderStatus
```

### RestApplication 注册

```java
EquipmentService equipmentService = new EquipmentService(ecf);
new EquipmentController(equipmentService).register(router);
```

---

## 前端设计

### 类型定义

**types/equipment.ts**:
```typescript
export interface Equipment {
  assetId: string;
  equipmentNumber: string;
  equipmentName: string;
  equipmentType?: string;
  facilityId?: string;
  facilityName?: string;
  status: string;
  acquiredDate?: string;
  description?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type EquipmentStatus = 'EqActive' | 'EqIdle' | 'EqMaintenance' | 'EqScrapped';

export const STATUS_LABELS: Record<EquipmentStatus, string> = {
  EqActive: '运行中', EqIdle: '停机', EqMaintenance: '维护中', EqScrapped: '报废',
};

export interface MaintenanceOrder {
  workEffortId: string;
  orderNumber: string;
  equipmentId: string;
  equipmentNumber?: string;
  equipmentName?: string;
  maintenanceType?: string;
  priority?: number;
  status: string;
  description?: string;
  scheduledDate?: string;
  completedDate?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type OrderStatus = 'MoPending' | 'MoInProgress' | 'MoCompleted';

export const ORDER_STATUS_LABELS: Record<OrderStatus, string> = {
  MoPending: '待处理', MoInProgress: '进行中', MoCompleted: '已完成',
};
```

### Store

**stores/equipment.ts**: 参考 productionRun.ts 模式
- 设备 CRUD actions + 状态变更
- 维护工单 CRUD actions + 状态变更
- API 路径：`/equipments` 和 `/maintenance-orders`

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| EquipmentListView.vue | ProductListView | 设备列表，带状态筛选 |
| EquipmentDetailView.vue | ProductDetailView | 设备详情 + 关联维护工单列表 |
| EquipmentFormDialog.vue | ProductFormDialog | 设备创建/编辑 |
| MaintenanceOrderListView.vue | ProductionRunListView | 维护工单列表 |
| MaintenanceOrderDetailView.vue | ProductionRunDetailView | 维护工单详情 |
| MaintenanceOrderFormDialog.vue | ProductionRunFormDialog | 维护工单创建/编辑 |
| EquipmentPanel.vue | ProductPanel | 面板组件 |

### 导航集成

新增 `asset` 子系统：

**navigation.ts:**
- SubsystemId 添加 `'asset'`
- ModuleId 添加 `'equipment'` | `'maintenance-order'`
- SUBSYSTEM_MODULE_MAP 添加 `asset: ['equipment', 'maintenance-order']`
- MODULE_SUBSYSTEM_MAP 添加 `'equipment': 'asset'`, `'maintenance-order': 'asset'`
- PanelContext 添加 `asset`, `equipment`, `'maintenance-order'`

**App.vue:**
- navItems 添加 asset 子系统（在 scm 之后）：
```javascript
{
  id: 'asset',
  label: '资产',
  icon: icons.wrench,
  description: '资产设备相关模块',
  type: 'subsystem',
  children: [
    { id: 'equipment', route: '/equipment', label: '设备管理', icon: icons.cog, description: '管理生产设备' },
    { id: 'maintenance-order', route: '/maintenance-order', label: '维护工单', icon: icons.clipboardList, description: '管理维护工单' },
  ],
}
```

**router/index.js:** 添加 equipment 和 maintenance-order 路由

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/EquipmentService.java` | 新增 |
| `backend/.../controller/EquipmentController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/equipment.ts` | 新增 |
| `frontend/src/renderer/stores/equipment.ts` | 新增 |
| `frontend/src/renderer/views/EquipmentListView.vue` | 新增 |
| `frontend/src/renderer/views/EquipmentDetailView.vue` | 新增 |
| `frontend/src/renderer/views/EquipmentFormDialog.vue` | 新增 |
| `frontend/src/renderer/views/MaintenanceOrderListView.vue` | 新增 |
| `frontend/src/renderer/views/MaintenanceOrderDetailView.vue` | 新增 |
| `frontend/src/renderer/views/MaintenanceOrderFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/EquipmentPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
| `frontend/src/renderer/utils/icons.js` | 修改（可选） |
