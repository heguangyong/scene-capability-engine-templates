---
name: return-rma-management
category: other
description: Template for Return Rma Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：51-00 退货管理

## 任务列表

- [x] 1. 后端 ReturnService
  - [x] 1.1 创建 ReturnService.java，实现退货申请 CRUD + 状态变更 + 退货明细 CRUD
  - [x] 1.2 实现退货编号自动生成 RMA-YYYYMMDD-XXXX

- [x] 2. 后端 ReturnController
  - [x] 2.1 创建 ReturnController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 ReturnService 和 ReturnController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/returnRma.ts 定义 ReturnOrder 和 ReturnItem 接口
  - [x] 4.2 创建 stores/returnRma.ts 实现退货申请和明细的 API 调用

- [x] 5. 前端视图 — 退货申请
  - [x] 5.1 创建 ReturnListView.vue 退货列表（分页 + 状态筛选）
  - [x] 5.2 创建 ReturnDetailView.vue 退货详情（含明细行列表）
  - [x] 5.3 创建 ReturnFormDialog.vue 退货创建/编辑表单

- [x] 6. 面板和导航集成
  - [x] 6.1 创建 ReturnPanel.vue 面板组件
  - [x] 6.2 修改 navigation.ts 添加 return-rma 模块到 sales 子系统
  - [x] 6.3 修改 ContextPanel.vue 添加面板映射
  - [x] 6.4 修改 App.vue 在 sales 子系统中添加退货导航
  - [x] 6.5 修改 router/index.js 添加路由

- [x] 7. 前端编译验证
  - [x] 7.1 运行 npm run build 确认编译通过

## 复验记录（{{DATE}}，工作区本地时间）

- `backend`: 首次执行 `mvn clean package -DskipTests` 失败，原因是 `CalendarService.java` 文件头包含 UTF-8 BOM；移除 BOM 后复跑同命令，最终通过。
- `frontend`: 执行 `npm run build` 通过。
- `frontend`: 执行 `npm test -- src/renderer/stores/__tests__/returnRma.test.ts` 通过（3/3）。
