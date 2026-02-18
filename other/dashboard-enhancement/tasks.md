---
name: dashboard-enhancement
category: other
description: Template for Dashboard Enhancement
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: {{SPEC_NAME_TITLE}}

## Overview

为 DashboardView 添加目录统计展示和快速导航。创建 Dashboard Pinia Store，修改 DashboardView.vue 新增三个区域。

## Tasks

- [x] 1. 创建 Dashboard Store
  - [x] 1.1 创建 `frontend/src/renderer/stores/dashboard.ts`
    - 定义 DashboardState 接口（catalog, loading, error）
    - 定义 ComponentBreakdownItem 接口
    - 实现 fetchCatalog action（调用 apiDiscoveryService.getCatalog，管理 loading/error 状态）
    - 实现 computed getters: totalEntities, totalServices, totalScreens, componentBreakdown
    - componentBreakdown 合并逻辑：遍历 catalog.components，从三个 catalog 的 components 中查找对应 count
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7_

  - [x]* 1.2 编写 Dashboard Store 单元测试
    - 测试初始状态（catalog=null, loading=false, error=null）
    - 测试 fetchCatalog 成功：loading 状态转换、catalog 数据存储
    - 测试 fetchCatalog 失败：error 存储、loading 恢复
    - 测试 getters 在 catalog=null 时返回 0/空数组
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5_

  - [x]* 1.3 编写 Dashboard Store Property Tests
    - **Property 1: Store 总数 getters 正确性**
    - 使用 fast-check 生成随机 ApiCatalogPayload，验证 totalEntities/totalServices/totalScreens 与 payload 中的值一致
    - **Validates: Requirements 1.6**

  - [x]* 1.4 编写 Component Breakdown Property Test
    - **Property 2: Component Breakdown 合并正确性**
    - 使用 fast-check 生成随机 ApiCatalogPayload（含任意数量 components），验证 componentBreakdown 正确合并三个 catalog 的 counts
    - **Validates: Requirements 1.7**

- [x] 2. Checkpoint - 确保 Store 测试通过
  - Ensure all tests pass, ask the user if questions arise.

- [x] 3. 修改 DashboardView.vue
  - [x] 3.1 添加 Catalog Stats Section
    - 引入 dashboard store 和 vue-router
    - onMounted 时调用 fetchCatalog（需认证状态）
    - 添加三个 Stat Card（Entity/Service/Screen），显示总数，点击跳转对应路由
    - 添加 loading skeleton 占位和 error + retry 按钮
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3, 3.4_

  - [x] 3.2 添加 Component Breakdown Section
    - 使用 `<details>` 元素，默认折叠
    - 展开后显示表格：组件名、Entity 数、Service 数、Screen 数
    - loading 时隐藏该区域
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [x] 3.3 添加 Quick Nav Section
    - 三个导航卡片（Entity/Service/Screen Explorer），含图标、标题、描述
    - 点击跳转对应路由
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

  - [x] 3.4 添加 Dark Theme 样式
    - 为所有新增区域添加 `[data-theme="dark"]` CSS 规则
    - 使用与现有 DashboardView 一致的 CSS 变量
    - _Requirements: 6.1, 6.2_

  - [x]* 3.5 编写 DashboardView 单元测试
    - 测试组件挂载后触发 fetchCatalog
    - 测试 stat cards 渲染和点击导航
    - 测试 loading/error 状态 UI
    - 测试 quick nav cards 渲染和点击导航
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3, 5.1, 5.3, 5.4, 5.5_

- [x] 4. Final Checkpoint - 确保所有测试通过
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- 标记 `*` 的子任务为可选测试任务
- 不新建子组件，所有 UI 直接嵌入 DashboardView
- 复用已有 apiDiscoveryService.getCatalog() 和 apiV1.ts 类型
- Property tests 使用 fast-check 库，每个 test 至少 100 次迭代
