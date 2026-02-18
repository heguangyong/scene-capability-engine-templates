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

# 任务清单：50-00 人力资源管理

## 任务列表

- [x] 1. 后端 HrService
  - [x] 1.1 创建 HrService.java，实现员工 CRUD + 部门 CRUD
  - [x] 1.2 实现员工编号自动生成 EMP-YYYYMMDD-XXXX
  - [x] 1.3 实现部门编号自动生成 DEPT-XXXX

- [x] 2. 后端 HrController
  - [x] 2.1 创建 HrController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 HrService 和 HrController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/hr.ts 定义 Employee 和 Department 接口
  - [x] 4.2 创建 stores/hr.ts 实现员工和部门的 API 调用

- [x] 5. 前端视图 — 员工
  - [x] 5.1 创建 EmployeeListView.vue 员工列表（分页 + 状态/部门筛选）
  - [x] 5.2 创建 EmployeeDetailView.vue 员工详情
  - [x] 5.3 创建 EmployeeFormDialog.vue 员工创建/编辑表单

- [x] 6. 前端视图 — 部门
  - [x] 6.1 创建 DepartmentListView.vue 部门列表
  - [x] 6.2 创建 DepartmentDetailView.vue 部门详情（含关联员工列表）
  - [x] 6.3 创建 DepartmentFormDialog.vue 部门创建/编辑表单

- [x] 7. 面板和导航集成
  - [x] 7.1 创建 HrPanel.vue 面板组件
  - [x] 7.2 修改 navigation.ts 添加 hr 子系统和 employee/department 模块
  - [x] 7.3 修改 ContextPanel.vue 添加面板映射
  - [x] 7.4 修改 App.vue 添加 hr 子系统导航
  - [x] 7.5 修改 router/index.js 添加路由

- [x] 8. 前端编译验证
  - [x] 8.1 运行 npm run build 确认编译通过
