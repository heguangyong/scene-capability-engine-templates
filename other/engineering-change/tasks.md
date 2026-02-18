---
name: engineering-change
category: other
description: Template for Engineering Change
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：49-00 工程变更管理

## 任务列表

- [x] 1. 后端 EngineeringChangeService
  - [x] 1.1 创建 EngineeringChangeService.java，实现 ECN CRUD + 状态变更
  - [x] 1.2 实现变更编号自动生成 ECN-YYYYMMDD-XXXX

- [x] 2. 后端 EngineeringChangeController
  - [x] 2.1 创建 EngineeringChangeController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 EngineeringChangeService 和 EngineeringChangeController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/engineeringChange.ts 定义 EngineeringChange 接口
  - [x] 4.2 创建 stores/engineeringChange.ts 实现 ECN API 调用

- [x] 5. 前端视图
  - [x] 5.1 创建 EngineeringChangeListView.vue ECN 列表（分页 + 状态筛选）
  - [x] 5.2 创建 EngineeringChangeDetailView.vue ECN 详情（含状态流转按钮）
  - [x] 5.3 创建 EngineeringChangeFormDialog.vue ECN 创建/编辑表单

- [x] 6. 面板和导航集成
  - [x] 6.1 创建 EngineeringChangePanel.vue 面板组件
  - [x] 6.2 修改 navigation.ts 添加 {{SPEC_NAME}} 模块到 mfg 子系统
  - [x] 6.3 修改 ContextPanel.vue 添加面板映射
  - [x] 6.4 修改 App.vue 添加 {{SPEC_NAME}} 导航项
  - [x] 6.5 修改 router/index.js 添加路由

- [x] 7. 前端编译验证
  - [x] 7.1 运行 npm run build 确认编译通过
