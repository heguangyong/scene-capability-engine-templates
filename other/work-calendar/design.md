---
name: work-calendar
category: other
description: Template for Work Calendar
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 工作日历 — 设计文档

## 架构概述

工作日历模块遵循项目全栈模式：后端 Service + Controller → 前端 types + store + views + panel → 导航集成。归属现有 `mfg` 子系统。

---

## 后端设计

### Entity 映射

**工作日历**: 使用 `mantle.work.effort.WorkEffort` entity
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetCalendar'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| calendarId | workEffortId | 主键 |
| calendarNumber | workEffortName | 日历编号 CAL-YYYY-XXXX |
| calendarName | description | 日历名称 |
| year | estimatedStartDate | 年份（存储为该年1月1日） |
| description | comments | 描述 |
| status | statusId | 状态 |

**班次**: 使用 `mantle.work.effort.WorkEffort` entity
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetShift'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| shiftId | workEffortId | 主键 |
| shiftNumber | workEffortName | 班次编号 SFT-XXXX |
| shiftName | description | 班次名称 |
| startTime | estimatedStartDate | 开始时间 |
| endTime | estimatedCompletionDate | 结束时间 |
| description | comments | 描述 |

**假日**: 使用 `mantle.work.effort.WorkEffort` entity
- 主键：`workEffortId`
- 通过 `workEffortTypeEnumId = 'WetHoliday'` 区分
- 通过 `WorkEffortAssoc` 关联到日历

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| holidayId | workEffortId | 主键 |
| holidayName | workEffortName | 假日名称 |
| holidayDate | estimatedStartDate | 假日日期 |
| holidayType | purposeEnumId | 假日类型 |
| calendarId | WorkEffortAssoc.toWorkEffortId | 关联日历 |

### 状态定义

日历状态：
| 状态 ID | 中文 |
|---------|------|
| CalActive | 启用 |
| CalInactive | 停用 |

假日类型：
| 类型 ID | 中文 |
|---------|------|
| HolLegal | 法定假日 |
| HolCompany | 公司假日 |
| HolAdjust | 调休 |

### CalendarService

```java
public class CalendarService {
    // 工作日历 CRUD
    public Map<String, Object> getCalendars(int pageIndex, int pageSize, String year);
    public Map<String, Object> getCalendarById(String calendarId);
    public Map<String, Object> createCalendar(Map<String, Object> data);
    public Map<String, Object> updateCalendar(String calendarId, Map<String, Object> data);
    public Map<String, Object> deleteCalendar(String calendarId);

    // 班次 CRUD
    public Map<String, Object> getShifts(int pageIndex, int pageSize);
    public Map<String, Object> getShiftById(String shiftId);
    public Map<String, Object> createShift(Map<String, Object> data);
    public Map<String, Object> updateShift(String shiftId, Map<String, Object> data);
    public Map<String, Object> deleteShift(String shiftId);

    // 假日 CRUD
    public Map<String, Object> getHolidays(String calendarId, String year, String type);
    public Map<String, Object> addHoliday(Map<String, Object> data);
    public Map<String, Object> deleteHoliday(String holidayId);

    // 编号生成
    public String generateCalendarNumber(ExecutionContext ec, String year);
    public String generateShiftNumber(ExecutionContext ec);
}
```

### CalendarController

路由注册：
```
GET    /api/v1/calendars                     → listCalendars
GET    /api/v1/calendars/{id}                → getCalendarDetail
POST   /api/v1/calendars                     → createCalendar
PUT    /api/v1/calendars/{id}                → updateCalendar
DELETE /api/v1/calendars/{id}                → deleteCalendar
GET    /api/v1/shifts                        → listShifts
GET    /api/v1/shifts/{id}                   → getShiftDetail
POST   /api/v1/shifts                        → createShift
PUT    /api/v1/shifts/{id}                   → updateShift
DELETE /api/v1/shifts/{id}                   → deleteShift
GET    /api/v1/holidays                      → listHolidays
POST   /api/v1/holidays                      → addHoliday
DELETE /api/v1/holidays/{id}                 → deleteHoliday
```

### RestApplication 注册

```java
CalendarService calendarService = new CalendarService(ecf);
new CalendarController(calendarService).register(router);
```

---

## 前端设计

### 类型定义

**types/calendar.ts**:
```typescript
export interface WorkCalendar {
  calendarId: string;
  calendarNumber: string;
  calendarName: string;
  year?: string;
  description?: string;
  status: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type CalendarStatus = 'CalActive' | 'CalInactive';

export const CALENDAR_STATUS_LABELS: Record<CalendarStatus, string> = {
  CalActive: '启用',
  CalInactive: '停用',
};

export const CALENDAR_STATUS_COLORS: Record<CalendarStatus, string> = {
  CalActive: '#22c55e',
  CalInactive: '#6b7280',
};

export interface Shift {
  shiftId: string;
  shiftNumber: string;
  shiftName: string;
  startTime?: string;
  endTime?: string;
  description?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export interface Holiday {
  holidayId: string;
  holidayName: string;
  holidayDate: string;
  holidayType?: string;
  calendarId?: string;
  createdDate?: string;
}

export type HolidayType = 'HolLegal' | 'HolCompany' | 'HolAdjust';

export const HOLIDAY_TYPE_LABELS: Record<HolidayType, string> = {
  HolLegal: '法定假日',
  HolCompany: '公司假日',
  HolAdjust: '调休',
};

export const HOLIDAY_TYPE_COLORS: Record<HolidayType, string> = {
  HolLegal: '#ef4444',
  HolCompany: '#3b82f6',
  HolAdjust: '#f59e0b',
};
```

### Store

**stores/calendar.ts**: 参考 equipment.ts 模式
- 工作日历 CRUD actions
- 班次 CRUD actions
- 假日 CRUD actions
- API 路径：`/calendars`、`/shifts`、`/holidays`

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| CalendarListView.vue | EquipmentListView | 日历列表，带年份筛选 |
| CalendarDetailView.vue | EquipmentDetailView | 日历详情 + 关联班次和假日 |
| CalendarFormDialog.vue | EquipmentFormDialog | 日历创建/编辑 |
| ShiftListView.vue | RoutingListView | 班次列表 |
| ShiftFormDialog.vue | RoutingFormDialog | 班次创建/编辑 |
| CalendarPanel.vue | EquipmentPanel | 面板组件 |

### 导航集成

归属现有 `mfg` 子系统：

**navigation.ts:**
- ModuleId 添加 `'{{SPEC_NAME}}'`
- SUBSYSTEM_MODULE_MAP 的 mfg 添加 `'{{SPEC_NAME}}'`
- MODULE_SUBSYSTEM_MAP 添加 `'{{SPEC_NAME}}': 'mfg'`
- PanelContext 添加 `'{{SPEC_NAME}}': Record<string, never>`
- DEFAULT_CONTEXTS 添加对应空对象
- currentPanelComponent 的 componentMap 添加 `'{{SPEC_NAME}}': 'CalendarPanel'`

**App.vue:**
- mfg 子系统的 children 中添加：
```javascript
{ id: '{{SPEC_NAME}}', route: '/{{SPEC_NAME}}', label: '工作日历', icon: icons.calendar, description: '管理生产日历和班次' }
```

**ContextPanel.vue:**
- 导入 CalendarPanel（defineAsyncComponent）
- panelComponentMap 添加 `'{{SPEC_NAME}}': CalendarPanel`
- subsystemConfigs 的 mfg 的 modules 中添加 {{SPEC_NAME}}

**router/index.js:**
```javascript
// 工作日历
{ path: '/{{SPEC_NAME}}', name: 'calendar-list', component: () => import('../views/CalendarListView.vue'), meta: { requiresAuth: true } },
{ path: '/{{SPEC_NAME}}/:calendarId', name: 'calendar-detail', component: () => import('../views/CalendarDetailView.vue'), meta: { requiresAuth: true } },
// 班次
{ path: '/shift', name: 'shift-list', component: () => import('../views/ShiftListView.vue'), meta: { requiresAuth: true } },
```

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/CalendarService.java` | 新增 |
| `backend/.../controller/CalendarController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/calendar.ts` | 新增 |
| `frontend/src/renderer/stores/calendar.ts` | 新增 |
| `frontend/src/renderer/views/CalendarListView.vue` | 新增 |
| `frontend/src/renderer/views/CalendarDetailView.vue` | 新增 |
| `frontend/src/renderer/views/CalendarFormDialog.vue` | 新增 |
| `frontend/src/renderer/views/ShiftListView.vue` | 新增 |
| `frontend/src/renderer/views/ShiftFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/CalendarPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
