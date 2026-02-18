---
name: production-run
category: other
description: Template for Production Run
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：生产工单管理

## 概述

新增生产工单（{{SPEC_NAME_TITLE}}）模块，包含后端 Service + Controller 和前端 Store + Views + Panel。遵循现有模块的架构模式（参考 BOM、Routing 等模块）。

## 数据模型

### ProductionRun（生产工单）

| 字段 | 类型 | 说明 |
|------|------|------|
| workEffortId | String | 主键，工单 ID |
| runNumber | String | 工单编号（PR-YYYYMMDD-XXXX） |
| productId | String | 产品 ID（必填） |
| productName | String | 产品名称（查询时关联） |
| bomId | String | BOM ID（必填） |
| bomName | String | BOM 名称（查询时关联） |
| routingId | String | 工艺路线 ID（可选） |
| quantity | BigDecimal | 计划生产数量（必填） |
| quantityProduced | BigDecimal | 已生产数量（默认 0） |
| statusId | String | 状态：WeCreated/WeScheduled/WeRunning/WeCompleted/WeCancelled |
| facilityId | String | 生产设施 ID（可选） |
| plannedStartDate | Timestamp | 计划开始日期（可选） |
| plannedEndDate | Timestamp | 计划结束日期（可选） |
| actualStartDate | Timestamp | 实际开始日期 |
| actualEndDate | Timestamp | 实际结束日期 |
| description | String | 备注 |
| createdDate | Timestamp | 创建时间 |
| lastUpdatedDate | Timestamp | 最后更新时间 |

### 状态流转

```
Created ──→ Scheduled ──→ Running ──→ Completed
  │              │
  └──→ Cancelled ←──┘
```

有效转换：
- Created → Scheduled, Cancelled
- Scheduled → Running, Cancelled
- Running → Completed
- Completed, Cancelled → （终态，不可变更）

## 后端设计

### ProductionRunService

```java
public class ProductionRunService {
    // CRUD
    Map<String, Object> getProductionRuns(int page, int size, String status);
    Map<String, Object> getProductionRunById(String id);
    Map<String, Object> createProductionRun(Map<String, Object> data);
    Map<String, Object> updateProductionRun(String id, Map<String, Object> data);
    void deleteProductionRun(String id);
    
    // 状态流转
    Map<String, Object> updateStatus(String id, String newStatus);
    
    // 辅助
    String generateRunNumber();  // PR-YYYYMMDD-XXXX
}
```

### ProductionRunController

```
GET    /api/v1/{{SPEC_NAME}}s          → 列表（分页 + 状态筛选）
GET    /api/v1/{{SPEC_NAME}}s/{id}     → 详情
POST   /api/v1/{{SPEC_NAME}}s          → 创建
PUT    /api/v1/{{SPEC_NAME}}s/{id}     → 更新
DELETE /api/v1/{{SPEC_NAME}}s/{id}     → 删除
PUT    /api/v1/{{SPEC_NAME}}s/{id}/status → 状态变更
```

### Moqui Entity 映射

使用 Moqui 的 WorkEffort entity，通过 workEffortTypeEnumId = 'WetProductionRun' 筛选生产工单。

## 前端设计

### TypeScript 类型 (types/productionRun.ts)

```typescript
export interface ProductionRun {
  workEffortId: string;
  runNumber: string;
  productId: string;
  productName?: string;
  bomId: string;
  bomName?: string;
  routingId?: string;
  quantity: number;
  quantityProduced: number;
  statusId: string;
  facilityId?: string;
  plannedStartDate?: string;
  plannedEndDate?: string;
  actualStartDate?: string;
  actualEndDate?: string;
  description?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type ProductionRunStatus = 'WeCreated' | 'WeScheduled' | 'WeRunning' | 'WeCompleted' | 'WeCancelled';

export const STATUS_LABELS: Record<ProductionRunStatus, string> = {
  WeCreated: '已创建',
  WeScheduled: '已排程',
  WeRunning: '生产中',
  WeCompleted: '已完成',
  WeCancelled: '已取消',
};

export const VALID_TRANSITIONS: Record<string, string[]> = {
  WeCreated: ['WeScheduled', 'WeCancelled'],
  WeScheduled: ['WeRunning', 'WeCancelled'],
  WeRunning: ['WeCompleted'],
};
```

### Pinia Store (stores/productionRun.ts)

遵循现有模块模式（参考 bom.ts），包含：
- state: items, currentItem, loading, error, pagination
- actions: fetchList, fetchById, create, update, remove, updateStatus

### 视图组件

| 组件 | 说明 |
|------|------|
| ProductionRunListView.vue | 列表页，状态筛选 + 分页 |
| ProductionRunDetailView.vue | 详情页，状态推进按钮 |
| ProductionRunFormDialog.vue | 创建/编辑对话框 |
| ProductionRunPanel.vue | ContextPanel 二级面板 |

### 路由

```javascript
{ path: '/{{SPEC_NAME}}', name: '{{SPEC_NAME}}-list', component: () => import('../views/ProductionRunListView.vue') },
{ path: '/{{SPEC_NAME}}/:runId', name: '{{SPEC_NAME}}-detail', component: () => import('../views/ProductionRunDetailView.vue') },
```

### 导航集成

- NavId 扩展：添加 '{{SPEC_NAME}}'（注意：如果 42-00 已完成，则加入 mfg 子系统的 children 中）
- PanelContext 扩展：添加 {{SPEC_NAME}}: Record<string, never>
- componentMap 扩展：添加 '{{SPEC_NAME}}': 'ProductionRunPanel'
- ContextPanel 面板映射：添加 ProductionRunPanel

## 测试策略

### 后端单元测试

- ProductionRunService 的 CRUD 操作
- 状态流转的有效/无效转换
- 工单编号生成格式

### 前端

- Store actions 的基本调用
- 状态流转按钮的条件显示

## 错误处理

1. 创建时 productId 或 bomId 无效 → 400 Bad Request
2. 更新/删除非 Created 状态的工单 → 400 Bad Request + 错误消息
3. 无效状态转换 → 400 Bad Request + 允许的目标状态列表
4. 工单不存在 → 404 Not Found
