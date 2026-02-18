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

# 设计文档：Dashboard 业务统计增强

## 概述

在现有 Dashboard 基础上新增业务统计区域和更新快速导航。后端新增一个统计聚合 API，前端扩展 dashboard store 和 DashboardView。

## 架构

### 后端

新增 `DashboardService.getBusinessStats()` 方法，聚合查询各业务模块的记录总数。通过现有的 `MonitoringController` 或新建端点暴露。

### API 设计

```
GET /api/v1/dashboard/stats
```

响应：
```json
{
  "products": 42,
  "orders": 156,
  "parties": 89,
  "invoices": 73,
  "procurements": 28,
  "inventory": 215,
  "boms": 15,
  "routings": 8,
  "facilities": 5,
  "shipments": 34
}
```

实现方式：对每个 entity 执行 `SELECT COUNT(*) FROM {entity}` 聚合查询，合并返回。

### 前端

扩展 `dashboard.ts` store：
- 新增 `businessStats` state
- 新增 `fetchBusinessStats()` action
- 调用 `/dashboard/stats` API

扩展 `DashboardView.vue`：
- 在目录统计下方新增"业务概览"区域
- 6 个主要统计卡片（产品、订单、Party、发票、采购、库存）
- 4 个次要统计卡片（BOM、工艺路线、设施、发货）
- 更新快速导航区域，添加业务模块入口

## 组件设计

### DashboardView 布局更新

```
┌─────────────────────────────────┐
│ Header + Welcome Card           │
├─────────────────────────────────┤
│ 业务概览（新增）                  │
│ ┌──────┐┌──────┐┌──────┐       │
│ │产品42││订单156││Party89│       │
│ └──────┘└──────┘└──────┘       │
│ ┌──────┐┌──────┐┌──────┐       │
│ │发票73││采购28 ││库存215│       │
│ └──────┘└──────┘└──────┘       │
├─────────────────────────────────┤
│ 目录统计（现有）                  │
├─────────────────────────────────┤
│ 快速导航（更新）                  │
│ 业务模块 + 开发工具              │
├─────────────────────────────────┤
│ 系统状态 + 平台信息（现有）       │
└─────────────────────────────────┘
```

### 统计卡片配置

```typescript
const businessStatCards = computed(() => [
  { label: '产品', count: businessStats.value?.products ?? 0, icon: icons.package, route: '/product', color: '#6366f1' },
  { label: '订单', count: businessStats.value?.orders ?? 0, icon: icons.shoppingCart, route: '/order', color: '#f59e0b' },
  { label: 'Party', count: businessStats.value?.parties ?? 0, icon: icons.users, route: '/party', color: '#22c55e' },
  { label: '发票', count: businessStats.value?.invoices ?? 0, icon: icons.receipt, route: '/invoice', color: '#ef4444' },
  { label: '采购', count: businessStats.value?.procurements ?? 0, icon: icons.clipboardList, route: '/procurement', color: '#8b5cf6' },
  { label: '库存', count: businessStats.value?.inventory ?? 0, icon: icons.box, route: '/inventory', color: '#06b6d4' },
]);
```

## 错误处理

1. 统计 API 失败 → 显示错误提示 + 重试按钮，不影响其他区域
2. 部分模块统计失败 → 对应卡片显示 "—"，其他正常显示
3. 未登录 → 不显示业务统计区域（与现有行为一致）
