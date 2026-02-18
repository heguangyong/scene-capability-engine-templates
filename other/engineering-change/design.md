---
name: engineering-change
category: other
description: Template for Engineering Change
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 工程变更管理 — 设计文档

## 架构概述

工程变更模块遵循项目全栈模式。扩展 `mfg` 子系统，新增 ECN CRUD 和状态流转。

---

## 后端设计

### Entity 映射

使用 `mantle.work.effort.WorkEffort` entity：
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetEngineeringChange'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| ecnId | workEffortId | 主键 |
| ecnNumber | workEffortName | 变更编号 ECN-YYYYMMDD-XXXX |
| title | description | 变更标题（description 前半段） |
| changeType | purposeEnumId | 变更类型 |
| priority | priority | 优先级 |
| status | statusId | 状态 |
| initiator | facilityId | 发起人（复用） |
| impactDescription | estimatedCompletionDate | 影响范围描述（复用为文本） |
| notes | description | 详细描述 |
| createdDate | estimatedStartDate | 创建日期 |
| completedDate | actualCompletionDate | 完成日期 |

### 状态定义

| 状态 ID | 中文 | 说明 |
|---------|------|------|
| EcnDraft | 草稿 | 初始状态 |
| EcnPendingApproval | 待审批 | 提交审批 |
| EcnApproved | 已批准 | 审批通过 |
| EcnInProgress | 执行中 | 正在执行变更 |
| EcnCompleted | 已完成 | 变更执行完毕 |
| EcnRejected | 已拒绝 | 审批拒绝 |

### EngineeringChangeService

```java
public class EngineeringChangeService {
    public Map<String, Object> getEcns(int pageIndex, int pageSize, String status);
    public Map<String, Object> getEcnById(String ecnId);
    public Map<String, Object> createEcn(Map<String, Object> data);
    public Map<String, Object> updateEcn(String ecnId, Map<String, Object> data);
    public Map<String, Object> deleteEcn(String ecnId);
    public Map<String, Object> changeStatus(String ecnId, String newStatus);
    public String generateEcnNumber(ExecutionContext ec);
}
```

### EngineeringChangeController

路由注册：
```
GET    /api/v1/{{SPEC_NAME}}s           → listEcns
GET    /api/v1/{{SPEC_NAME}}s/{id}      → getEcnDetail
POST   /api/v1/{{SPEC_NAME}}s           → createEcn
PUT    /api/v1/{{SPEC_NAME}}s/{id}      → updateEcn
DELETE /api/v1/{{SPEC_NAME}}s/{id}      → deleteEcn
PUT    /api/v1/{{SPEC_NAME}}s/{id}/status → changeStatus
```

### RestApplication 注册

```java
EngineeringChangeService engineeringChangeService = new EngineeringChangeService(ecf);
new EngineeringChangeController(engineeringChangeService).register(router);
```

---

## 前端设计

### 类型定义 (types/engineeringChange.ts)

```typescript
export interface EngineeringChange {
  workEffortId: string;
  ecnNumber: string;
  title: string;
  changeType?: string;
  priority?: number;
  status: string;
  initiator?: string;
  impactDescription?: string;
  notes?: string;
  createdDate?: string;
  completedDate?: string;
  lastUpdatedDate?: string;
}

export type EcnStatus = 'EcnDraft' | 'EcnPendingApproval' | 'EcnApproved' | 'EcnInProgress' | 'EcnCompleted' | 'EcnRejected';

export const STATUS_LABELS: Record<EcnStatus, string> = {
  EcnDraft: '草稿',
  EcnPendingApproval: '待审批',
  EcnApproved: '已批准',
  EcnInProgress: '执行中',
  EcnCompleted: '已完成',
  EcnRejected: '已拒绝',
};

export const STATUS_COLORS: Record<EcnStatus, string> = {
  EcnDraft: '#6b7280',
  EcnPendingApproval: '#f59e0b',
  EcnApproved: '#3b82f6',
  EcnInProgress: '#8b5cf6',
  EcnCompleted: '#22c55e',
  EcnRejected: '#ef4444',
};

export type ChangeType = 'bom_change' | 'routing_change' | 'material_substitution';

export const CHANGE_TYPE_LABELS: Record<ChangeType, string> = {
  bom_change: 'BOM 变更',
  routing_change: '工艺变更',
  material_substitution: '材料替代',
};
```

### Store (stores/engineeringChange.ts)

参考 productionRun.ts 模式，API 路径 `/{{SPEC_NAME}}s`。
额外 action：`changeStatus(id, newStatus)` — PUT /{{SPEC_NAME}}s/{id}/status

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| EngineeringChangeListView.vue | ProductionRunListView | ECN 列表，带状态筛选 |
| EngineeringChangeDetailView.vue | ProductionRunDetailView | ECN 详情 + 状态流转按钮 |
| EngineeringChangeFormDialog.vue | ProductionRunFormDialog | 创建/编辑表单 |
| EngineeringChangePanel.vue | ProductionRunPanel | 面板组件 |

### 导航集成

**navigation.ts:**
- ModuleId 添加 `'{{SPEC_NAME}}'`
- SUBSYSTEM_MODULE_MAP.mfg 添加 `'{{SPEC_NAME}}'`
- MODULE_SUBSYSTEM_MAP 添加 `'{{SPEC_NAME}}': 'mfg'`
- PanelContext 添加 `'{{SPEC_NAME}}': Record<string, never>`

**App.vue:**
- navItems mfg.children 添加：
```javascript
{ id: '{{SPEC_NAME}}', route: '/{{SPEC_NAME}}', label: '工程变更', icon: icons.fileEdit, description: '管理工程变更通知' }
```

**router/index.js:** 添加 {{SPEC_NAME}} 路由

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/EngineeringChangeService.java` | 新增 |
| `backend/.../controller/EngineeringChangeController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/engineeringChange.ts` | 新增 |
| `frontend/src/renderer/stores/engineeringChange.ts` | 新增 |
| `frontend/src/renderer/views/EngineeringChangeListView.vue` | 新增 |
| `frontend/src/renderer/views/EngineeringChangeDetailView.vue` | 新增 |
| `frontend/src/renderer/views/EngineeringChangeFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/EngineeringChangePanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
