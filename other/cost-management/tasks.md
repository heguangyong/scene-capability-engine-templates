---
name: cost-management
category: other
description: Template for Cost Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：48-00 成本管理

## 任务列表

- [x] 1. 后端 CostService
  - [x] 1.1 创建 CostService.java，实现成本记录 CRUD + 差异计算
  - [x] 1.2 实现记录编号自动生成 CR-YYYYMMDD-XXXX

- [x] 2. 后端 CostController
  - [x] 2.1 创建 CostController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 CostService 和 CostController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/cost.ts 定义 CostRecord 接口
  - [x] 4.2 创建 stores/cost.ts 实现成本记录 API 调用

- [x] 5. 前端视图
  - [x] 5.1 创建 CostRecordListView.vue 成本记录列表（分页 + 类型筛选）
  - [x] 5.2 创建 CostRecordDetailView.vue 成本详情（含差异分析展示）
  - [x] 5.3 创建 CostRecordFormDialog.vue 成本记录创建/编辑表单

- [x] 6. 面板和导航集成
  - [x] 6.1 创建 CostPanel.vue 面板组件
  - [x] 6.2 修改 navigation.ts 添加 cost 模块到 finance 子系统
  - [x] 6.3 修改 ContextPanel.vue 添加面板映射
  - [x] 6.4 修改 App.vue 添加 cost 导航项
  - [x] 6.5 修改 router/index.js 添加路由

- [x] 7. 前端编译验证
  - [x] 7.1 运行 npm run build 确认编译通过
