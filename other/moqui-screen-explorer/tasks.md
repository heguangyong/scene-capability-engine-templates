---
name: moqui-screen-explorer
category: other
description: Template for Moqui Screen Explorer
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: {{SPEC_NAME_TITLE}}

## Overview

基于已有的后端 Screen REST API，在 Vue 3 前端中构建 Screen Explorer 模块。按照自底向上的顺序实现：类型定义 → API 服务 → Store → 组件 → 路由/导航集成。

## Tasks

- [x] 1. 创建 Screen TypeScript 类型定义和 API 服务层
  - [x] 1.1 创建 `frontend/src/renderer/types/screen.ts`，定义 ScreenSummary、ScreenDefinitionData、ScreenDefinitionDetail、ScreenParameter、ScreenTransition、ScreenWidget、ScreenForm、ScreenFormField、ScreenFormsData、ScreenWidgetsData、ScreenListData、ScreenListParams 类型
    - 类型需匹配后端 ScreenSummary.toMap()、ScreenService 和 ScreenCatalogService 的响应格式
    - _Requirements: 8.1, 8.2, 8.3, 8.4, 8.5, 8.6_
  - [x] 1.2 创建 `frontend/src/renderer/services/screenApiService.ts`，实现 listScreens、getScreenDefinition、getScreenForms、getScreenWidgets 方法
    - 复用 apiV1Client 实例，遵循 entityService.ts 的模式
    - listScreens 支持 component、keyword、pageIndex、pageSize 可选参数
    - 其他方法接收 catalogPath 参数，作为 query parameter 传递
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6_
  - [x]* 1.3 创建 `frontend/src/renderer/services/__tests__/screenApiService.spec.ts`，编写 API 服务属性测试
    - **Property 1: API 服务端点映射正确性**
    - 使用 fast-check 生成随机参数组合，mock apiV1Client，验证调用的路径和参数正确
    - **Validates: Requirements 1.1, 1.2, 1.3, 1.4**

- [x] 2. 创建 Screen Pinia Store
  - [x] 2.1 创建 `frontend/src/renderer/stores/screen.ts`，实现 screenStore
    - State: screens、pagination、listLoading、listError、currentComponent、currentKeyword、currentPageIndex、currentPageSize、currentScreen、currentForms、currentWidgets、detailLoading、detailError
    - Actions: fetchScreens、fetchScreenDetail、fetchScreenForms、fetchScreenWidgets、setFilter、setPage、clearDetail
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8_
  - [x]* 2.2 创建 `frontend/src/renderer/stores/__tests__/screenStore.spec.ts`，编写 Store 属性测试
    - **Property 2: Store 获取操作状态机**
    - 使用 fast-check 生成随机 API 响应（成功/失败），验证 loading/error/data 状态转换正确
    - **Validates: Requirements 2.3, 2.4, 2.5, 2.6, 2.7, 2.8**

- [x] 3. Checkpoint — 确保服务层和 Store 测试通过
  - 运行 `npm test`，确保所有测试通过，如有问题请询问用户。

- [x] 4. 创建 Screen Explorer 视图和子组件
  - [x] 4.1 创建 `frontend/src/renderer/components/screens/ScreenCatalogBrowser.vue`
    - 搜索框（keyword 输入）+ 组件筛选下拉 + Screen 列表 + 分页控件
    - 从 screenStore 读取数据，调用 setFilter/setPage/fetchScreens
    - 点击条目通过 router.push 导航到 `/screens/{catalogPath}`
    - 加载中显示 spinner，错误时显示错误信息和重试按钮
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6, 3.7_
  - [x] 4.2 创建 `frontend/src/renderer/components/screens/ScreenDetailPanel.vue`
    - 展示 Screen 基本信息（名称、路径、组件）
    - 参数列表表格（名称、是否必填）
    - 转换列表表格（名称、关联服务）
    - _Requirements: 4.2, 4.3_
  - [x] 4.3 创建 `frontend/src/renderer/components/screens/FormInspector.vue`
    - 表单列表，每个表单可展开/折叠显示字段详情
    - 字段表格：字段名、类型
    - 无表单时显示空状态提示
    - _Requirements: 5.1, 5.2, 5.3, 5.4_
  - [x] 4.4 创建 `frontend/src/renderer/components/screens/WidgetTreeViewer.vue`
    - 递归树形组件，展示 Widget 层次
    - 每个节点显示类型和关键属性，点击展开/折叠
    - 使用缩进表示层次关系
    - 无 Widget 时显示空状态提示
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5_
  - [x] 4.5 创建 `frontend/src/renderer/views/ScreenListView.vue`
    - 顶层路由视图，包含 ScreenCatalogBrowser
    - 进入时触发 fetchScreens
    - _Requirements: 3.1_
  - [x] 4.6 创建 `frontend/src/renderer/views/ScreenDetailView.vue`
    - 顶层路由视图，包含标签页切换（概览/表单/Widget）
    - 从路由参数获取 catalogPath，触发 fetchScreenDetail
    - 包含 ScreenDetailPanel、FormInspector、WidgetTreeViewer
    - 加载中显示 spinner，错误时显示错误信息和返回列表链接
    - _Requirements: 4.1, 4.4, 4.5, 4.6_

- [x] 5. 路由和导航集成
  - [x] 5.1 修改 `frontend/src/renderer/router/index.js`，添加 `/screens` 和 `/screens/:catalogPath(.*)` 路由
    - 两个路由都设置 `meta: { requiresAuth: true }`
    - ScreenListView 立即加载，ScreenDetailView 懒加载
    - _Requirements: 7.1, 7.2, 7.5_
  - [x] 5.2 修改 `frontend/src/renderer/stores/navigation.ts`，添加 screens 导航项
    - 扩展 NavId 类型添加 'screens'
    - 在 NAV_ITEMS 中添加 Screen 探索条目
    - 扩展 PanelContext 和 DEFAULT_CONTEXTS
    - _Requirements: 7.3_
  - [x]* 5.3 编写组件属性测试
    - 创建 `frontend/src/renderer/components/__tests__/WidgetTreeViewer.spec.ts`
    - **Property 7: Widget 树完整渲染**
    - 使用 fast-check 生成随机 Widget 树结构，验证所有节点类型都被渲染
    - **Validates: Requirements 6.1, 6.2**

- [x] 6. Final checkpoint — 确保所有测试通过
  - 运行 `npm test`，确保所有测试通过（包括原有 27 个测试），如有问题请询问用户。

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- 所有组件使用 Vue 3 Composition API (`<script setup lang="ts">`)
- 样式遵循现有的 CSS 变量和深色主题支持模式
- Property tests 使用 fast-check 库，最少 100 次迭代
- 后端 Screen API 已完整实现，本 Spec 仅涉及前端开发
