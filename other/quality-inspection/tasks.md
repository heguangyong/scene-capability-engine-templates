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

# 质量检验管理 — 任务清单

## 任务列表

- [x] 1. 后端 QualityInspectionService
  - [x] 1.1 创建 `QualityInspectionService.java`，实现 CRUD + 结果提交
  - [x] 1.2 实现 `getInspections(page, size, resultStatus)` 分页查询
  - [x] 1.3 实现 `getInspectionById(id)` 详情查询，关联工单和产品信息
  - [x] 1.4 实现 `createInspection(data)` 创建检验记录，生成编号 QI-YYYYMMDD-XXXX
  - [x] 1.5 实现 `updateInspection(id, data)` 更新（仅 QiPending 状态）
  - [x] 1.6 实现 `deleteInspection(id)` 删除（仅 QiPending 状态）
  - [x] 1.7 实现 `submitResult(id, result, notes)` 提交检验结果

- [x] 2. 后端 QualityInspectionController
  - [x] 2.1 创建 `QualityInspectionController.java`，注册 6 个路由
  - [x] 2.2 在 `RestApplication.java` 中注册 Service 和 Controller

- [x] 3. 后端编译验证
  - [x] 3.1 运行 `cd backend && mvn clean package -DskipTests` 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 `types/qualityInspection.ts` 定义接口和常量
  - [x] 4.2 创建 `stores/qualityInspection.ts` 实现 API 调用和状态管理

- [x] 5. 前端视图
  - [x] 5.1 创建 `QualityInspectionListView.vue` 列表视图
  - [x] 5.2 创建 `QualityInspectionDetailView.vue` 详情视图（含结果提交）
  - [x] 5.3 创建 `QualityInspectionFormDialog.vue` 表单对话框

- [x] 6. 面板和导航集成
  - [x] 6.1 创建 `QualityInspectionPanel.vue`
  - [x] 6.2 修改 `navigation.ts`：添加 ModuleId、映射、PanelContext
  - [x] 6.3 修改 `ContextPanel.vue`：导入面板、添加映射、更新 subsystemConfigs
  - [x] 6.4 修改 `App.vue`：mfg.children 添加质量检验条目
  - [x] 6.5 修改 `router/index.js`：添加路由

- [x] 7. 前端编译验证
  - [x] 7.1 运行 `cd frontend && npm run build` 确认编译通过
