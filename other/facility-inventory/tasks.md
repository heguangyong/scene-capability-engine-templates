---
name: facility-inventory
category: other
description: Template for Facility Inventory
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 库存设施管理模块 — 任务列表

## 任务

- [x] 1. 后端 FacilityService 实现
  - [x] 1.1 创建 FacilityService.java，实现 listFacilities, getFacilityDetail, createFacility, updateFacility
  - [x] 1.2 实现库存资产查询: listAssets
  - [x] 1.3 实现设施类型查询: listFacilityTypes
  - [x] 1.4 实现 errorResult 辅助方法

- [x] 2. 后端 FacilityController 实现
  - [x] 2.1 创建 FacilityController.java，注册所有路由
  - [x] 2.2 实现请求参数解析和响应处理
  - [x] 2.3 在 RestApplication.java 中注册 FacilityService 和 FacilityController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 `mvn clean package`（从 backend/ 目录），确保编译通过

- [x] 4. 前端类型定义
  - [x] 4.1 创建 `frontend/src/renderer/types/facility.ts`

- [x] 5. 前端 API 服务
  - [x] 5.1 创建 `frontend/src/renderer/services/facilityApiService.ts`

- [x] 6. 前端 Pinia Store
  - [x] 6.1 创建 `frontend/src/renderer/stores/facility.ts`

- [x] 7. 前端视图组件
  - [x] 7.1 创建 `frontend/src/renderer/views/FacilityListView.vue`
  - [x] 7.2 创建 `frontend/src/renderer/views/FacilityDetailView.vue`
  - [x] 7.3 创建 `frontend/src/renderer/views/FacilityFormDialog.vue`
  - [x] 7.4 创建 `frontend/src/renderer/components/panels/FacilityPanel.vue`

- [x] 8. 前端编译验证
  - [x] 8.1 运行 `npm run build`（从 frontend/ 目录），确保编译通过

- [x]* 9. 导航集成（⚠️ 暂不执行，由 Kiro 统一处理）
  - [x]* 9.1 App.vue navItems 添加 facility
  - [x]* 9.2 navigation.ts 添加 facility NavId
  - [x]* 9.3 ContextPanel 注册 FacilityPanel
  - [x]* 9.4 router/index.js 添加 facility 路由
  - [x]* 9.5 icons.js 导入 Warehouse 图标
