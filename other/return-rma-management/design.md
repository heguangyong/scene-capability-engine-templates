---
name: return-rma-management
category: other
description: Template for Return Rma Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 退货管理 — 设计文档

## 架构概述

退货管理模块遵循项目全栈模式：后端 Service + Controller → 前端 types + store + views + panel → 导航集成。归属现有 `sales` 子系统。

---

## 后端设计

### Entity 映射

**退货申请**: 使用 `mantle.order.return.ReturnHeader` entity
- 主键：`returnId`

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| returnId | returnId | 主键 |
| returnNumber | comments | 退货编号 RMA-YYYYMMDD-XXXX |
| orderId | entryDate 复用不合适，用 ReturnItem.orderId | 关联订单 |
| customerId | fromPartyId | 客户 |
| returnReason | description | 退货原因 |
| status | statusId | 退货状态 |
| requestDate | entryDate | 申请日期 |
| description | comments 已用，用 needsInventoryReceive 复用 | 描述（简化） |

**简化方案**: 由于 ReturnHeader 字段有限，采用以下映射：
| 业务字段 | Entity 字段 |
|----------|-------------|
| returnId | returnId |
| returnNumber | ReturnHeader.comments |
| customerId | fromPartyId |
| returnReason | description |
| status | statusId |
| requestDate | entryDate |

**退货明细**: 使用 `mantle.order.return.ReturnItem` entity
- 主键：`returnId` + `returnItemSeqId`

字段映射：
| 业务字段 | Entity 字段 |
|----------|-------------|
| returnItemSeqId | returnItemSeqId |
| orderId | orderId |
| productId | productId |
| quantity | returnQuantity |
| returnReason | returnReasonEnumId |
| refundAmount | returnPrice |
| status | statusId |

### 状态定义

退货状态：
| 状态 ID | 中文 |
|---------|------|
| RmaPending | 待审批 |
| RmaApproved | 已批准 |
| RmaRejected | 已拒绝 |
| RmaCompleted | 已完成 |

### ReturnService

```java
public class ReturnService {
    // 退货申请 CRUD
    public Map<String, Object> getReturns(int pageIndex, int pageSize, String status);
    public Map<String, Object> getReturnById(String returnId);
    public Map<String, Object> createReturn(Map<String, Object> data);
    public Map<String, Object> updateReturn(String returnId, Map<String, Object> data);
    public Map<String, Object> deleteReturn(String returnId);
    public Map<String, Object> changeStatus(String returnId, String newStatus);

    // 退货明细
    public Map<String, Object> getReturnItems(String returnId);
    public Map<String, Object> addReturnItem(String returnId, Map<String, Object> data);
    public Map<String, Object> deleteReturnItem(String returnId, String itemSeqId);

    // 编号生成
    public String generateReturnNumber(ExecutionContext ec);
}
```

### ReturnController

路由注册：
```
GET    /api/v1/returns                       → listReturns
GET    /api/v1/returns/{id}                  → getReturnDetail
POST   /api/v1/returns                       → createReturn
PUT    /api/v1/returns/{id}                  → updateReturn
DELETE /api/v1/returns/{id}                  → deleteReturn
PUT    /api/v1/returns/{id}/status           → changeStatus
GET    /api/v1/returns/{id}/items            → getReturnItems
POST   /api/v1/returns/{id}/items            → addReturnItem
DELETE /api/v1/returns/{id}/items/{itemSeqId} → deleteReturnItem
```

### RestApplication 注册

```java
ReturnService returnService = new ReturnService(ecf);
new ReturnController(returnService).register(router);
```

---

## 前端设计

### 类型定义

**types/returnRma.ts**:
```typescript
export interface ReturnOrder {
  returnId: string;
  returnNumber: string;
  customerId?: string;
  customerName?: string;
  returnReason?: string;
  status: string;
  requestDate?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type ReturnStatus = 'RmaPending' | 'RmaApproved' | 'RmaRejected' | 'RmaCompleted';

export const RETURN_STATUS_LABELS: Record<ReturnStatus, string> = {
  RmaPending: '待审批',
  RmaApproved: '已批准',
  RmaRejected: '已拒绝',
  RmaCompleted: '已完成',
};

export const RETURN_STATUS_COLORS: Record<ReturnStatus, string> = {
  RmaPending: '#f59e0b',
  RmaApproved: '#3b82f6',
  RmaRejected: '#ef4444',
  RmaCompleted: '#22c55e',
};

export interface ReturnItem {
  returnItemSeqId: string;
  orderId?: string;
  productId?: string;
  productName?: string;
  quantity: number;
  returnReason?: string;
  refundAmount?: number;
  status?: string;
}
```

### Store

**stores/returnRma.ts**: 参考 equipment.ts 模式
- 退货申请 CRUD actions + 状态变更
- 退货明细 CRUD actions
- API 路径：`/returns` 和 `/returns/{id}/items`

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| ReturnListView.vue | OrderListView | 退货列表，带状态筛选 |
| ReturnDetailView.vue | OrderDetailView | 退货详情 + 明细行列表 |
| ReturnFormDialog.vue | OrderFormDialog | 退货创建/编辑 |
| ReturnPanel.vue | OrderPanel（复用） | 面板组件 |

### 导航集成

归属现有 `sales` 子系统：

**navigation.ts:**
- ModuleId 添加 `'return-rma'`
- SUBSYSTEM_MODULE_MAP 的 sales 添加 `'return-rma'`
- MODULE_SUBSYSTEM_MAP 添加 `'return-rma': 'sales'`
- PanelContext 添加 `'return-rma': Record<string, never>`
- DEFAULT_CONTEXTS 添加对应空对象
- currentPanelComponent 的 componentMap 添加 `'return-rma': 'ReturnPanel'`

**App.vue:**
- sales 子系统的 children 中添加：
```javascript
{ id: 'return-rma', route: '/return-rma', label: '退货管理', icon: icons.undo, description: '管理退货申请' }
```

**ContextPanel.vue:**
- 导入 ReturnPanel（defineAsyncComponent）
- panelComponentMap 添加 `'return-rma': ReturnPanel`
- subsystemConfigs 的 sales 的 modules 中添加 return-rma

**router/index.js:**
```javascript
// 退货管理
{ path: '/return-rma', name: 'return-list', component: () => import('../views/ReturnListView.vue'), meta: { requiresAuth: true } },
{ path: '/return-rma/:returnId', name: 'return-detail', component: () => import('../views/ReturnDetailView.vue'), meta: { requiresAuth: true } },
```

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/ReturnService.java` | 新增 |
| `backend/.../controller/ReturnController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/returnRma.ts` | 新增 |
| `frontend/src/renderer/stores/returnRma.ts` | 新增 |
| `frontend/src/renderer/views/ReturnListView.vue` | 新增 |
| `frontend/src/renderer/views/ReturnDetailView.vue` | 新增 |
| `frontend/src/renderer/views/ReturnFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/ReturnPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
