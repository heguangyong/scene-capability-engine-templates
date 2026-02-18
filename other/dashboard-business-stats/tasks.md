---
name: dashboard-business-stats
category: other
description: Template for Dashboard Business Stats
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实施计划：Dashboard 业务统计增强

## 概述

后端新增统计聚合 API，前端扩展 dashboard store 和 DashboardView，添加业务统计卡片和更新快速导航。

## 任务

- [x] 1. 后端统计 API
  - [x] 1.1 创建 `backend/app/src/main/java/org/moqui/rest/service/DashboardService.java`
    - 实现 getBusinessStats() 方法
    - 对每个业务 entity 执行 COUNT 查询：Product, OrderHeader, Party, Invoice, PurchaseOrder, InventoryItem, BillOfMaterials, Routing, Facility, Shipment
    - 返回 Map<String, Integer> 包含各模块记录总数
    - 参考现有 Service 的 Moqui ExecutionContext 使用模式
    - _Requirements: 2.1-2.4_

  - [x] 1.2 创建 `backend/app/src/main/java/org/moqui/rest/controller/DashboardController.java`
    - GET /api/v1/dashboard/stats → 调用 DashboardService.getBusinessStats()
    - 返回 JSON 格式的统计数据
    - _Requirements: 2.1, 2.4_

- [x] 2. 后端编译验证
  - 运行 `cd backend && mvn clean package -DskipTests` 确保编译通过

- [x] 3. 前端 Store 扩展
  - [x] 3.1 扩展 `frontend/src/renderer/stores/dashboard.ts`
    - 新增 businessStats ref: Record<string, number> | null
    - 新增 businessStatsLoading ref
    - 新增 businessStatsError ref
    - 新增 fetchBusinessStats() action — 调用 GET /dashboard/stats
    - 保持现有 fetchCatalog() 不变
    - _Requirements: 1.4_

- [x] 4. 前端视图更新
  - [x] 4.1 更新 `frontend/src/renderer/views/DashboardView.vue`
    - 在 Welcome Card 下方、目录统计上方新增"业务概览"区域
    - 使用 lucide-vue-next 图标组件（不用 emoji）
    - 6 个主要统计卡片：产品、订单、Party、发票、采购、库存
    - 点击卡片导航到对应模块列表页
    - 加载时显示骨架屏
    - 错误时显示重试按钮
    - 更新快速导航区域：添加业务模块入口，原有 Explorer 移至"开发工具"子区域
    - 在 onMounted 中调用 fetchBusinessStats()
    - _Requirements: 1.1-1.4, 3.1-3.3_

- [x] 5. 前端编译验证
  - 运行 `cd frontend && npm run build` 确保编译通过

## 备注

- 后端统计查询使用 Moqui 的 entity-find count 模式
- 前端图标使用 lucide-vue-next 组件，与侧边栏一致
- 不修改现有的目录统计功能，只新增业务统计
- 统计 API 和目录 API 独立调用，互不影响
