---
name: production-run
category: other
description: Template for Production Run
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实施计划：生产工单管理

## 概述

新增生产工单模块，后端 Service + Controller + Tests，前端 Types + API + Store + Views + Panel + 导航集成。

## 任务

- [x] 1. 后端 Service 层
  - [x] 1.1 创建 `backend/app/src/main/java/org/moqui/rest/service/ProductionRunService.java`
    - 参考 BomService.java 的模式
    - 实现 getProductionRuns(page, size, status) — 使用 WorkEffort entity，workEffortTypeEnumId='WetProductionRun'
    - 实现 getProductionRunById(id) — 返回工单详情，关联产品名称和 BOM 名称
    - 实现 createProductionRun(data) — 必填 productId, quantity, bomId；自动生成 runNumber（PR-YYYYMMDD-XXXX）；初始状态 WeCreated
    - 实现 updateProductionRun(id, data) — 仅 WeCreated 状态可编辑
    - 实现 deleteProductionRun(id) — 仅 WeCreated 状态可删除
    - 实现 updateStatus(id, newStatus) — 验证状态转换有效性
    - 实现 generateRunNumber() — 格式 PR-YYYYMMDD-XXXX
    - _Requirements: 1.1-1.6, 2.1-2.4_

- [x] 2. 后端 Controller 层
  - [x] 2.1 创建 `backend/app/src/main/java/org/moqui/rest/controller/ProductionRunController.java`
    - 参考 BomController.java 的模式
    - GET /api/v1/{{SPEC_NAME}}s — 列表，query params: page, size, status
    - GET /api/v1/{{SPEC_NAME}}s/{id} — 详情
    - POST /api/v1/{{SPEC_NAME}}s — 创建
    - PUT /api/v1/{{SPEC_NAME}}s/{id} — 更新
    - DELETE /api/v1/{{SPEC_NAME}}s/{id} — 删除
    - PUT /api/v1/{{SPEC_NAME}}s/{id}/status — 状态变更，body: { statusId: "WeScheduled" }
    - _Requirements: 1.1-1.5, 2.1-2.4_

- [x] 3. 后端编译验证
  - 运行 `cd backend && mvn clean package -DskipTests` 确保编译通过
  - 如有错误修复后再继续

- [x] 4. 前端类型和 API
  - [x] 4.1 创建 `frontend/src/renderer/types/productionRun.ts`
    - ProductionRun 接口
    - ProductionRunStatus 类型
    - STATUS_LABELS 常量
    - VALID_TRANSITIONS 常量
    - ProductionRunListResponse, ProductionRunCreateRequest 等接口
    - _Requirements: 1.1, 2.1_

  - [x] 4.2 创建 `frontend/src/renderer/stores/productionRun.ts`
    - 参考 bom.ts 的模式
    - state: items, currentItem, loading, error, pagination
    - actions: fetchList, fetchById, create, update, remove, updateStatus
    - API 路径: /{{SPEC_NAME}}s
    - _Requirements: 1.1-1.5, 2.1_

- [x] 5. 前端视图
  - [x] 5.1 创建 `frontend/src/renderer/views/ProductionRunListView.vue`
    - 参考 BomListView.vue 的模式
    - 表格列：工单编号、产品、数量、状态、创建时间
    - 状态筛选下拉（全部/已创建/已排程/生产中/已完成/已取消）
    - 分页组件
    - 创建按钮
    - _Requirements: 3.1-3.4_

  - [x] 5.2 创建 `frontend/src/renderer/views/ProductionRunDetailView.vue`
    - 参考 BomDetailView.vue 的模式
    - 显示工单基本信息
    - 状态推进按钮（根据 VALID_TRANSITIONS 显示可用操作）
    - 编辑/删除按钮（仅 WeCreated 状态）
    - _Requirements: 4.1-4.4_

  - [x] 5.3 创建 `frontend/src/renderer/views/ProductionRunFormDialog.vue`
    - 参考 BomFormDialog.vue 的模式
    - 产品选择下拉（从 /products API 获取）
    - BOM 选择下拉（从 /boms API 获取，可按产品筛选）
    - 数量输入（正整数）
    - 计划开始日期（可选）
    - 备注（可选）
    - 表单验证
    - _Requirements: 5.1-5.5_

- [x] 6. 前端面板和导航集成
  - [x] 6.1 创建 `frontend/src/renderer/components/panels/ProductionRunPanel.vue`
    - 参考 BomPanel.vue 的模式
    - 显示工单相关快捷操作

  - [x] 6.2 导航集成
    - 在 `navigation.ts` 中：扩展 NavId 添加 '{{SPEC_NAME}}'，扩展 PanelContext，扩展 DEFAULT_CONTEXTS，扩展 componentMap
    - 在 `ContextPanel.vue` 中：导入 ProductionRunPanel，添加到 panelComponentMap
    - 在 `App.vue` 中：在 navItems 的 mfg 子系统 children 中添加 {{SPEC_NAME}} 条目（如果 42-00 已完成），否则在扁平 navItems 中添加
    - 在 `router/index.js` 中：添加 /{{SPEC_NAME}} 和 /{{SPEC_NAME}}/:runId 路由
    - 在 `icons.js` 中：如需新图标则添加（可使用 Cog 或 Play 图标）
    - _Requirements: 6.1-6.3_

- [x] 7. 前端编译验证
  - 运行 `cd frontend && npm run build` 确保编译通过
  - 如有错误修复后再继续

## 备注

- 参考现有模块（BOM、Routing）的代码模式，保持一致性
- 后端使用 Moqui WorkEffort entity，通过 workEffortTypeEnumId 区分生产工单
- 如果 Spec 42-00（子系统导航重构）已完成，导航集成时将 {{SPEC_NAME}} 加入 mfg 子系统的 children
- 如果 42-00 未完成，先在扁平 navItems 中添加，后续由 42-00 统一重构
