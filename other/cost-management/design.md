---
name: cost-management
category: other
description: Template for Cost Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 成本管理 — 设计文档

## 架构概述

成本管理模块遵循项目全栈模式。扩展 `finance` 子系统，新增成本记录 CRUD 和差异分析。

---

## 后端设计

### Entity 映射

使用 `mantle.product.cost.ProductCost` entity（如果不存在，使用 `mantle.work.effort.WorkEffort` 替代）。

优先方案 — 使用 WorkEffort entity：
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetCostRecord'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| costRecordId | workEffortId | 主键 |
| recordNumber | workEffortName | 记录编号 CR-YYYYMMDD-XXXX |
| productId | facilityId | 关联产品（复用） |
| costType | purposeEnumId | 成本类型 |
| standardAmount | estimatedWorkDuration | 标准金额（复用数值字段） |
| actualAmount | actualWorkDuration | 实际金额（复用数值字段） |
| currency | description | 币种（存在 description 前缀） |
| effectiveDate | estimatedStartDate | 有效期 |
| notes | description | 描述 |
| status | statusId | 状态 |

### 状态定义

| 状态 ID | 中文 |
|---------|------|
| CrDraft | 草稿 |
| CrActive | 生效 |
| CrArchived | 归档 |

### CostService

```java
public class CostService {
    public Map<String, Object> getCostRecords(int pageIndex, int pageSize, String costType);
    public Map<String, Object> getCostRecordById(String costRecordId);
    public Map<String, Object> createCostRecord(Map<String, Object> data);
    public Map<String, Object> updateCostRecord(String costRecordId, Map<String, Object> data);
    public Map<String, Object> deleteCostRecord(String costRecordId);
    public String generateRecordNumber(ExecutionContext ec);
}
```

差异计算在 `toRecordMap` 辅助方法中完成：
```java
double variance = actualAmount - standardAmount;
double variancePercent = standardAmount != 0 ? (variance / standardAmount) * 100 : 0;
result.put("variance", variance);
result.put("variancePercent", variancePercent);
```

### CostController

路由注册：
```
GET    /api/v1/cost-records           → listCostRecords
GET    /api/v1/cost-records/{id}      → getCostRecordDetail
POST   /api/v1/cost-records           → createCostRecord
PUT    /api/v1/cost-records/{id}      → updateCostRecord
DELETE /api/v1/cost-records/{id}      → deleteCostRecord
```

### RestApplication 注册

```java
CostService costService = new CostService(ecf);
new CostController(costService).register(router);
```

---

## 前端设计

### 类型定义 (types/cost.ts)

```typescript
export interface CostRecord {
  workEffortId: string;
  recordNumber: string;
  productId?: string;
  productName?: string;
  costType?: string;
  standardAmount: number;
  actualAmount: number;
  variance: number;
  variancePercent: number;
  currency?: string;
  effectiveDate?: string;
  notes?: string;
  status: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type CostType = 'material' | 'labor' | 'overhead';

export const COST_TYPE_LABELS: Record<CostType, string> = {
  material: '材料成本',
  labor: '人工成本',
  overhead: '制造费用',
};

export const STATUS_LABELS: Record<string, string> = {
  CrDraft: '草稿',
  CrActive: '生效',
  CrArchived: '归档',
};
```

### Store (stores/cost.ts)

参考 productionRun.ts 模式，API 路径 `/cost-records`。

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| CostRecordListView.vue | InvoiceListView | 成本记录列表，带类型筛选 |
| CostRecordDetailView.vue | InvoiceDetailView | 成本详情 + 差异分析展示 |
| CostRecordFormDialog.vue | InvoiceFormDialog | 创建/编辑表单 |
| CostPanel.vue | InvoicePanel | 面板组件（如果存在） |

### 导航集成

**navigation.ts:**
- ModuleId 添加 `'cost'`
- SUBSYSTEM_MODULE_MAP.finance 添加 `'cost'`
- MODULE_SUBSYSTEM_MAP 添加 `'cost': 'finance'`
- PanelContext 添加 `'cost': Record<string, never>`

**App.vue:**
- navItems finance.children 添加：
```javascript
{ id: 'cost', route: '/cost', label: '成本管理', icon: icons.calculator, description: '管理成本记录' }
```

**router/index.js:** 添加 cost 路由

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/CostService.java` | 新增 |
| `backend/.../controller/CostController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/cost.ts` | 新增 |
| `frontend/src/renderer/stores/cost.ts` | 新增 |
| `frontend/src/renderer/views/CostRecordListView.vue` | 新增 |
| `frontend/src/renderer/views/CostRecordDetailView.vue` | 新增 |
| `frontend/src/renderer/views/CostRecordFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/CostPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
