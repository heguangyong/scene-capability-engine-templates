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

# 需求文档：Dashboard 业务统计增强

## 简介

当前 Dashboard 只显示 Moqui 目录统计（Entity/Service/Screen 数量）和快速导航。本次增强将添加业务模块的关键统计数据，让用户一眼了解业务运营状况。

## 需求

### 需求 1：业务统计卡片

**用户故事：** 作为 ERP 用户，我希望在 Dashboard 看到各业务模块的关键数据，以便快速了解运营状况。

#### 验收标准

1. THE Dashboard SHALL 显示业务统计区域，包含以下统计卡片：
   - 产品总数（来自 /api/v1/products）
   - 订单总数（来自 /api/v1/orders）
   - Party 总数（来自 /api/v1/parties）
   - 发票总数（来自 /api/v1/invoices）
   - 采购单总数（来自 /api/v1/procurements）
   - 库存项总数（来自 /api/v1/inventory）
2. EACH 统计卡片 SHALL 显示图标、数字和标签
3. WHEN 用户点击统计卡片, THE System SHALL 导航到对应模块的列表页
4. THE 统计数据 SHALL 在 Dashboard 加载时异步获取，显示加载骨架屏

### 需求 2：后端统计 API

**用户故事：** 作为前端开发者，我希望有一个统一的统计 API，以便一次请求获取所有业务统计数据。

#### 验收标准

1. THE System SHALL 提供 GET /api/v1/dashboard/stats API
2. THE API SHALL 返回各模块的记录总数
3. THE API SHALL 在 3 秒内响应（聚合查询）
4. THE API 响应格式 SHALL 为：
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

### 需求 3：快速导航更新

**用户故事：** 作为用户，我希望 Dashboard 的快速导航包含业务模块入口，而不仅仅是目录浏览器。

#### 验收标准

1. THE Dashboard 快速导航 SHALL 包含业务模块入口（产品、订单、Party、发票等）
2. THE 快速导航卡片 SHALL 使用与侧边栏一致的图标
3. THE 原有的 Entity/Service/Screen Explorer 快速导航 SHALL 保留，移至"开发工具"区域
