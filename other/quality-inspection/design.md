---
name: quality-inspection
category: other
description: Template for Quality Inspection
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 质量检验管理 — 设计文档

## 架构概述

质量检验模块遵循项目现有的全栈模式：后端 Service + Controller → 前端 types + store + views + panel → 导航集成。

---

## 后端设计

### Entity 映射

使用 Moqui `mantle.work.effort.WorkEffort` entity，通过 `workEffortTypeEnumId` 区分：
- 质量检验类型：`WetQualityInspection`（自定义枚举值）
- 主键：`workEffortId`

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| inspectionId | workEffortId | 主键 |
| inspectionNumber | workEffortName | 检验编号 QI-YYYYMMDD-XXXX |
| productionRunId | parentWorkEffortId | 关联生产工单 |
| inspectionType | purposeEnumId | 检验类型 |
| resultStatus | statusId | 检验结果状态 |
| inspector | assignedPartyId | 检验员 |
| description | description | 备注 |
| inspectionDate | estimatedStartDate | 检验日期 |
| completedDate | actualCompletionDate | 完成日期 |

### 状态定义

| 状态 ID | 中文 | 说明 |
|---------|------|------|
| QiPending | 待检 | 初始状态 |
| QiPassed | 通过 | 检验合格 |
| QiFailed | 不通过 | 检验不合格 |
| QiConditionalPass | 有条件通过 | 部分合格 |

### QualityInspectionService

```java
public class QualityInspectionService {
    // 常量
    private static final String INSPECTION_TYPE_ENUM_ID = "WetQualityInspection";
    private static final String STATUS_PENDING = "QiPending";
    
    // 方法
    public Map<String, Object> getInspections(int pageIndex, int pageSize, String resultStatus);
    public Map<String, Object> getInspectionById(String inspectionId);
    public Map<String, Object> createInspection(Map<String, Object> data);
    public Map<String, Object> updateInspection(String inspectionId, Map<String, Object> data);
    public Map<String, Object> deleteInspection(String inspectionId);
    public Map<String, Object> submitResult(String inspectionId, String result, String notes);
    public String generateInspectionNumber(ExecutionContext ec);
}
```

### QualityInspectionController

路由注册：
```
GET    /api/v1/{{SPEC_NAME}}s           → listInspections
GET    /api/v1/{{SPEC_NAME}}s/{id}      → getInspectionDetail
POST   /api/v1/{{SPEC_NAME}}s           → createInspection
PUT    /api/v1/{{SPEC_NAME}}s/{id}      → updateInspection
DELETE /api/v1/{{SPEC_NAME}}s/{id}      → deleteInspection
PUT    /api/v1/{{SPEC_NAME}}s/{id}/result → submitResult
```

### RestApplication 注册

```java
QualityInspectionService qualityInspectionService = new QualityInspectionService(ecf);
new QualityInspectionController(qualityInspectionService).register(router);
```

---

## 前端设计

### 类型定义 (types/qualityInspection.ts)

```typescript
export interface QualityInspection {
  workEffortId: string;
  inspectionNumber: string;
  productionRunId?: string;
  productionRunNumber?: string;
  productId?: string;
  productName?: string;
  inspectionType?: string;
  resultStatus: string;
  inspector?: string;
  description?: string;
  inspectionDate?: string;
  completedDate?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type InspectionResult = 'QiPending' | 'QiPassed' | 'QiFailed' | 'QiConditionalPass';

export const RESULT_LABELS: Record<InspectionResult, string>;
export const RESULT_COLORS: Record<InspectionResult, string>;
```

### Store (stores/qualityInspection.ts)

参考 productionRun.ts 模式，API 路径 `/{{SPEC_NAME}}s`。
额外 action：`submitResult(id, result, notes)` — PUT /{{SPEC_NAME}}s/{id}/result

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| QualityInspectionListView.vue | ProductionRunListView | 列表，带结果筛选 |
| QualityInspectionDetailView.vue | ProductionRunDetailView | 详情，带结果提交按钮 |
| QualityInspectionFormDialog.vue | ProductionRunFormDialog | 创建/编辑表单 |
| QualityInspectionPanel.vue | ProductionRunPanel | 面板组件 |

### 导航集成

navigation.ts 变更：
- ModuleId 添加 `'{{SPEC_NAME}}'`
- SUBSYSTEM_MODULE_MAP.mfg 添加 `'{{SPEC_NAME}}'`
- MODULE_SUBSYSTEM_MAP 添加 `'{{SPEC_NAME}}': 'mfg'`
- PanelContext 添加 `'{{SPEC_NAME}}': Record<string, never>`
- componentMap 添加 `'{{SPEC_NAME}}': 'QualityInspectionPanel'`

ContextPanel.vue 变更：
- 导入 QualityInspectionPanel
- panelComponentMap 添加映射
- subsystemConfigs.mfg.modules 添加质量检验条目

App.vue 变更：
- navItems mfg.children 添加质量检验条目
- 图标使用 `icons.check`（Check 图标代表质量检验）

router/index.js 变更：
- 添加 `/{{SPEC_NAME}}` 和 `/{{SPEC_NAME}}/:inspectionId` 路由

### 图标

需要在 icons.js 中新增导入：
- `ClipboardCheck` from lucide-vue-next（如果可用），否则使用 `Check`

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/QualityInspectionService.java` | 新增 |
| `backend/.../controller/QualityInspectionController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改（注册 Service + Controller） |
| `frontend/src/renderer/types/qualityInspection.ts` | 新增 |
| `frontend/src/renderer/stores/qualityInspection.ts` | 新增 |
| `frontend/src/renderer/views/QualityInspectionListView.vue` | 新增 |
| `frontend/src/renderer/views/QualityInspectionDetailView.vue` | 新增 |
| `frontend/src/renderer/views/QualityInspectionFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/QualityInspectionPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
| `frontend/src/renderer/utils/icons.js` | 修改（可选，新增图标） |
