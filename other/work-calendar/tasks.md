---
name: work-calendar
category: other
description: Template for Work Calendar
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：52-00 工作日历

## 任务列表

- [x] 1. 后端 CalendarService
  - [x] 1.1 创建 CalendarService.java，实现工作日历 CRUD + 班次 CRUD + 假日 CRUD
  - [x] 1.2 实现日历编号自动生成 CAL-YYYY-XXXX
  - [x] 1.3 实现班次编号自动生成 SFT-XXXX

- [x] 2. 后端 CalendarController
  - [x] 2.1 创建 CalendarController.java，注册所有 REST 路由
  - [x] 2.2 修改 RestApplication.java 注册 CalendarService 和 CalendarController

- [x] 3. 后端编译验证
  - [x] 3.1 运行 mvn clean package -DskipTests 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 types/calendar.ts 定义 WorkCalendar、Shift、Holiday 接口
  - [x] 4.2 创建 stores/calendar.ts 实现日历、班次、假日的 API 调用

- [x] 5. 前端视图 — 工作日历
  - [x] 5.1 创建 CalendarListView.vue 日历列表（分页 + 年份筛选）
  - [x] 5.2 创建 CalendarDetailView.vue 日历详情（含关联班次和假日列表）
  - [x] 5.3 创建 CalendarFormDialog.vue 日历创建/编辑表单

- [x] 6. 前端视图 — 班次
  - [x] 6.1 创建 ShiftListView.vue 班次列表
  - [x] 6.2 创建 ShiftFormDialog.vue 班次创建/编辑表单

- [x] 7. 面板和导航集成
  - [x] 7.1 创建 CalendarPanel.vue 面板组件
  - [x] 7.2 修改 navigation.ts 添加 {{SPEC_NAME}} 模块到 mfg 子系统
  - [x] 7.3 修改 ContextPanel.vue 添加面板映射
  - [x] 7.4 修改 App.vue 在 mfg 子系统中添加工作日历导航
  - [x] 7.5 修改 router/index.js 添加路由

- [x] 8. 前端编译验证
  - [x] 8.1 运行 npm run build 确认编译通过
