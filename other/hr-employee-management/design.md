---
name: hr-employee-management
category: other
description: Template for Hr Employee Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 人力资源管理 — 设计文档

## 架构概述

人力资源管理模块遵循项目全栈模式：后端 Service + Controller → 前端 types + store + views + panel → 导航集成。新增 `hr` 子系统。

---

## 后端设计

### Entity 映射

**员工**: 使用 `mantle.humanres.employment.Employment` + `mantle.party.Party` entity
- 主键：`partyId`（Party 表）
- 通过 `partyTypeEnumId = 'PtyPerson'` + `roleTypeId = 'Employee'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| employeeId | partyId | 主键（Party） |
| employeeNumber | Party.comments | 员工编号 EMP-YYYYMMDD-XXXX |
| firstName | Person.firstName | 名 |
| lastName | Person.lastName | 姓 |
| departmentId | Employment.facilityId | 部门（复用 facilityId） |
| position | Employment.comments | 职位 |
| hireDate | Employment.fromDate | 入职日期 |
| status | Party.statusId | 状态 |
| phone | PartyContactMech → TelecomNumber | 联系电话 |
| email | PartyContactMech → ContactMech.infoString | 邮箱 |

**部门**: 使用 `mantle.party.PartyGroup` entity
- 主键：`partyId`
- 通过 `partyTypeEnumId = 'PtyOrganization'` + `roleTypeId = 'Department'` 区分

字段映射：
| 业务字段 | Entity 字段 | 说明 |
|----------|-------------|------|
| departmentId | partyId | 主键 |
| departmentNumber | PartyGroup.comments | 部门编号 DEPT-XXXX |
| departmentName | PartyGroup.groupName | 部门名称 |
| parentDepartmentId | PartyRelationship.toPartyId | 上级部门 |
| managerId | PartyRelationship.toPartyId (Manager) | 负责人 |
| description | PartyGroup.description | 描述 |

### 状态定义

员工状态：
| 状态 ID | 中文 |
|---------|------|
| EmActive | 在职 |
| EmResigned | 离职 |
| EmProbation | 试用期 |

### HrService

```java
public class HrService {
    // 员工 CRUD
    public Map<String, Object> getEmployees(int pageIndex, int pageSize, String status, String departmentId);
    public Map<String, Object> getEmployeeById(String employeeId);
    public Map<String, Object> createEmployee(Map<String, Object> data);
    public Map<String, Object> updateEmployee(String employeeId, Map<String, Object> data);
    public Map<String, Object> deleteEmployee(String employeeId);

    // 部门 CRUD
    public Map<String, Object> getDepartments(int pageIndex, int pageSize);
    public Map<String, Object> getDepartmentById(String departmentId);
    public Map<String, Object> createDepartment(Map<String, Object> data);
    public Map<String, Object> updateDepartment(String departmentId, Map<String, Object> data);
    public Map<String, Object> deleteDepartment(String departmentId);

    // 编号生成
    public String generateEmployeeNumber(ExecutionContext ec);
    public String generateDepartmentNumber(ExecutionContext ec);
}
```

### HrController

路由注册：
```
GET    /api/v1/employees                     → listEmployees
GET    /api/v1/employees/{id}                → getEmployeeDetail
POST   /api/v1/employees                     → createEmployee
PUT    /api/v1/employees/{id}                → updateEmployee
DELETE /api/v1/employees/{id}                → deleteEmployee
GET    /api/v1/departments                   → listDepartments
GET    /api/v1/departments/{id}              → getDepartmentDetail
POST   /api/v1/departments                   → createDepartment
PUT    /api/v1/departments/{id}              → updateDepartment
DELETE /api/v1/departments/{id}              → deleteDepartment
```

### RestApplication 注册

```java
HrService hrService = new HrService(ecf);
new HrController(hrService).register(router);
```

---

## 前端设计

### 类型定义

**types/hr.ts**:
```typescript
export interface Employee {
  employeeId: string;
  employeeNumber: string;
  firstName: string;
  lastName: string;
  departmentId?: string;
  departmentName?: string;
  position?: string;
  hireDate?: string;
  status: string;
  phone?: string;
  email?: string;
  createdDate?: string;
  lastUpdatedDate?: string;
}

export type EmployeeStatus = 'EmActive' | 'EmResigned' | 'EmProbation';

export const EMPLOYEE_STATUS_LABELS: Record<EmployeeStatus, string> = {
  EmActive: '在职',
  EmResigned: '离职',
  EmProbation: '试用期',
};

export const EMPLOYEE_STATUS_COLORS: Record<EmployeeStatus, string> = {
  EmActive: '#22c55e',
  EmResigned: '#ef4444',
  EmProbation: '#f59e0b',
};

export interface Department {
  departmentId: string;
  departmentNumber: string;
  departmentName: string;
  parentDepartmentId?: string;
  parentDepartmentName?: string;
  managerId?: string;
  managerName?: string;
  description?: string;
  employeeCount?: number;
  createdDate?: string;
  lastUpdatedDate?: string;
}
```

### Store

**stores/hr.ts**: 参考 equipment.ts 模式
- 员工 CRUD actions
- 部门 CRUD actions
- API 路径：`/employees` 和 `/departments`

### 视图组件

| 组件 | 参考 | 说明 |
|------|------|------|
| EmployeeListView.vue | PartyListView | 员工列表，带状态/部门筛选 |
| EmployeeDetailView.vue | PartyDetailView | 员工详情 |
| EmployeeFormDialog.vue | PartyFormDialog | 员工创建/编辑 |
| DepartmentListView.vue | FacilityListView | 部门列表 |
| DepartmentDetailView.vue | FacilityDetailView | 部门详情 + 关联员工列表 |
| DepartmentFormDialog.vue | FacilityFormDialog | 部门创建/编辑 |
| HrPanel.vue | EquipmentPanel | 面板组件 |

### 导航集成

新增 `hr` 子系统：

**navigation.ts:**
- SubsystemId 添加 `'hr'`
- ModuleId 添加 `'employee'` | `'department'`
- SUBSYSTEM_MODULE_MAP 添加 `hr: ['employee', 'department']`
- MODULE_SUBSYSTEM_MAP 添加 `'employee': 'hr'`, `'department': 'hr'`
- PanelContext 添加 `hr`, `employee`, `department`

**App.vue:**
- navItems 添加 hr 子系统（在 org 之后）：
```javascript
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
```

**router/index.js:** 添加 employee 和 department 路由

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/HrService.java` | 新增 |
| `backend/.../controller/HrController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/hr.ts` | 新增 |
| `frontend/src/renderer/stores/hr.ts` | 新增 |
| `frontend/src/renderer/views/EmployeeListView.vue` | 新增 |
| `frontend/src/renderer/views/EmployeeDetailView.vue` | 新增 |
| `frontend/src/renderer/views/EmployeeFormDialog.vue` | 新增 |
| `frontend/src/renderer/views/DepartmentListView.vue` | 新增 |
| `frontend/src/renderer/views/DepartmentDetailView.vue` | 新增 |
| `frontend/src/renderer/views/DepartmentFormDialog.vue` | 新增 |
| `frontend/src/renderer/components/panels/HrPanel.vue` | 新增 |
| `frontend/src/renderer/stores/navigation.ts` | 修改 |
| `frontend/src/renderer/components/panels/ContextPanel.vue` | 修改 |
| `frontend/src/renderer/App.vue` | 修改 |
| `frontend/src/renderer/router/index.js` | 修改 |
