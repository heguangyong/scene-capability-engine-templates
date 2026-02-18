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

# 任务清单：47-00 设备管理

## 任务列表

- [x] 1. 后端 EquipmentService
  - [x] 1.1 创建 EquipmentService.java，实现设备 CRUD + 状态变更 + 维护工单 CRUD
  - [x] 1.2 实现设备编号自动生成 EQ-YYYYMMDD-XXXX
  - [x] 1.3 实现维护工单编号自动生成 MO-YYYYMMDD-XXXX

- [x] 2. 后端 EquipmentController
  - [x] 2.1 创建 EquipmentController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 EquipmentService 和 EquipmentController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/equipment.ts 定义 Equipment 和 MaintenanceOrder 接口
  - [x] 4.2 创建 stores/equipment.ts 实现设备和维护工单的 API 调用

- [x] 5. 前端视图 — 设备
  - [x] 5.1 创建 EquipmentListView.vue 设备列表（分页 + 状态筛选）
  - [x] 5.2 创建 EquipmentDetailView.vue 设备详情（含关联维护工单列表）
  - [x] 5.3 创建 EquipmentFormDialog.vue 设备创建/编辑表单

- [x] 6. 前端视图 — 维护工单
  - [x] 6.1 创建 MaintenanceOrderListView.vue 维护工单列表
  - [x] 6.2 创建 MaintenanceOrderDetailView.vue 维护工单详情
  - [x] 6.3 创建 MaintenanceOrderFormDialog.vue 维护工单创建/编辑表单

- [x] 7. 面板和导航集成
  - [x] 7.1 创建 EquipmentPanel.vue 面板组件
  - [x] 7.2 修改 navigation.ts 添加 asset 子系统和 equipment/maintenance-order 模块
  - [x] 7.3 修改 ContextPanel.vue 添加面板映射
  - [x] 7.4 修改 App.vue 添加 asset 子系统导航
  - [x] 7.5 修改 router/index.js 添加路由

- [x] 8. 前端编译验证
  - [x] 8.1 运行 npm run build 确认编译通过
