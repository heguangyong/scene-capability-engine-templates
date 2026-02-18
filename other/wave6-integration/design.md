---
name: wave6-integration
category: other
description: Template for Wave6 Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Wave 6 多模块导航集成

## 概述

将 3 个 Wave 6 业务模块（人力资源、退货、工作日历）集成到前端导航系统和后端注册。纯配置性集成，在共享文件中追加配置项。

## 架构

复用已有的三层联动导航架构 + 后端 Service/Controller 注册模式。

## 修改文件清单

| 文件 | 修改内容 |
|------|---------|
| `RestApplication.java` | 注册 3 个 Service + 3 个 Controller |
| `navigation.ts` | SubsystemId/ModuleId/Maps/PanelContext/DEFAULT_CONTEXTS/componentMap |
| `ContextPanel.vue` | defineAsyncComponent + panelComponentMap + subsystemConfigs |
| `App.vue` | navItems 添加 hr 子系统，扩展 sales 和 mfg |
| `router/index.js` | 追加 9 条路由 |
| `icons.js` | 确保 Users, UserCheck, Building, Undo2, Calendar 图标已导入 |

## 具体配置

### RestApplication.java

```java
// Wire services 区域添加
HrService hrService = new HrService(ecf);
ReturnService returnService = new ReturnService(ecf);
CalendarService calendarService = new CalendarService(ecf);

// Wire controllers 区域添加
new HrController(hrService).register(router);
new ReturnController(returnService).register(router);
new CalendarController(calendarService).register(router);
```

### navigation.ts

```typescript
// SubsystemId 扩展
export type SubsystemId = 'mfg' | 'scm' | 'asset' | 'finance' | 'sales' | 'org' | 'hr';

// ModuleId 扩展
export type ModuleId = ... | 'employee' | 'department' | 'return-rma' | 'work-calendar';

// SUBSYSTEM_MODULE_MAP 扩展
hr: ['employee', 'department'],
sales: ['order', 'return-rma'],  // 追加 return-rma
mfg: [..., 'work-calendar'],     // 追加 work-calendar

// MODULE_SUBSYSTEM_MAP 追加
'employee': 'hr',
'department': 'hr',
'return-rma': 'sales',
'work-calendar': 'mfg',
```

### App.vue navItems

```javascript
// 新增 hr 子系统（在 org 之后）
{
  id: 'hr',
  label: '人力资源',
  icon: icons.users,
  description: '人力资源相关模块',
  type: 'subsystem',
  children: [
    { id: 'employee', route: '/employee', label: '员工管理', icon: icons.userCheck, description: '管理员工档案' },
    { id: 'department', route: '/department', label: '部门管理', icon: icons.building, description: '管理组织架构' },
  ],
}

// sales 子系统 children 追加
{ id: 'return-rma', route: '/return-rma', label: '退货管理', icon: icons.undo, description: '管理退货申请' }

// mfg 子系统 children 追加
{ id: 'work-calendar', route: '/work-calendar', label: '工作日历', icon: icons.calendar, description: '管理生产日历和班次' }
```

### router/index.js 新增 9 条路由

```javascript
// 员工管理
{ path: '/employee', name: 'employee-list', component: () => import('../views/EmployeeListView.vue'), meta: { requiresAuth: true } },
{ path: '/employee/:employeeId', name: 'employee-detail', component: () => import('../views/EmployeeDetailView.vue'), meta: { requiresAuth: true } },
// 部门管理
{ path: '/department', name: 'department-list', component: () => import('../views/DepartmentListView.vue'), meta: { requiresAuth: true } },
{ path: '/department/:departmentId', name: 'department-detail', component: () => import('../views/DepartmentDetailView.vue'), meta: { requiresAuth: true } },
// 退货管理
{ path: '/return-rma', name: 'return-list', component: () => import('../views/ReturnListView.vue'), meta: { requiresAuth: true } },
{ path: '/return-rma/:returnId', name: 'return-detail', component: () => import('../views/ReturnDetailView.vue'), meta: { requiresAuth: true } },
// 工作日历
{ path: '/work-calendar', name: 'calendar-list', component: () => import('../views/CalendarListView.vue'), meta: { requiresAuth: true } },
{ path: '/work-calendar/:calendarId', name: 'calendar-detail', component: () => import('../views/CalendarDetailView.vue'), meta: { requiresAuth: true } },
// 班次
{ path: '/shift', name: 'shift-list', component: () => import('../views/ShiftListView.vue'), meta: { requiresAuth: true } },
```

## 测试策略

编译验证为核心质量保证手段：
- 前端：`npm run build`
- 后端：`mvn clean package -DskipTests`
