---
name: subsystem-navigation
category: other
description: Template for Subsystem Navigation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档

## 简介

当前 ERP 系统侧边栏有 11 个一级导航图标（dashboard + 10 个业务模块），已接近空间上限。本次重构将扁平导航结构改为子系统分组的二级导航，参考 Moqui 原始子系统设计。侧边栏只显示 7 个一级图标（概览、制造、供应链、财务、销售、组织、系统），点击子系统图标后 ContextPanel 显示该子系统的模块列表，用户选择具体模块后再切换到对应的业务面板。

## 术语表

- **Sidebar（侧边栏）**: 应用左侧极窄图标栏，显示一级导航图标
- **Subsystem（子系统）**: 一级导航分组，如制造、供应链、财务等
- **Module（模块）**: 子系统下的具体业务模块，如产品、BOM、工艺路线等
- **ContextPanel（上下文面板）**: 侧边栏右侧的中间面板，显示当前导航上下文内容
- **SubsystemPanel（子系统面板）**: ContextPanel 中显示子系统模块列表的通用组件
- **NavId**: 导航标识符类型，用于标识当前激活的导航项
- **navItems**: App.vue 中定义的侧边栏导航项数组

## 需求

### 需求 1：子系统分组导航结构

**用户故事：** 作为 ERP 用户，我希望侧边栏按子系统分组显示导航，以便在模块增多时仍能快速定位目标功能。

#### 验收标准

1. THE Sidebar SHALL 显示 7 个一级导航图标：概览(Dashboard)、制造(MFG)、供应链(SCM)、财务(Finance)、销售(Sales)、组织(Organization)、系统(Admin)
2. WHEN 用户点击"概览"图标, THE Sidebar SHALL 直接导航到 Dashboard 页面并在 ContextPanel 显示 DashboardPanel
3. WHEN 用户点击"系统"图标, THE Sidebar SHALL 导航到 Admin 页面并在 ContextPanel 显示 AdminPanel
4. WHEN 用户点击制造/供应链/财务/销售/组织子系统图标, THE ContextPanel SHALL 显示该子系统的 SubsystemPanel，列出其下属模块
5. THE navItems 数据结构 SHALL 从扁平数组改为嵌套结构，每个子系统项包含 children 数组定义其下属模块

### 需求 2：子系统面板交互

**用户故事：** 作为 ERP 用户，我希望点击子系统后能看到该子系统下的所有模块列表，并能快速进入具体模块。

#### 验收标准

1. THE SubsystemPanel SHALL 接收子系统配置（名称、描述、模块列表）并渲染模块列表
2. WHEN 用户在 SubsystemPanel 中点击某个模块, THE System SHALL 导航到该模块的路由，并将 ContextPanel 切换为该模块的业务面板
3. WHEN SubsystemPanel 显示模块列表时, THE SubsystemPanel SHALL 为每个模块显示图标、名称和描述
4. WHEN 用户从模块面板返回子系统面板, THE ContextPanel SHALL 恢复显示该子系统的 SubsystemPanel

### 需求 3：子系统分组配置

**用户故事：** 作为开发者，我希望子系统分组关系在代码中清晰定义，以便后续扩展新模块。

#### 验收标准

1. THE Navigation Store SHALL 定义子系统与模块的映射关系，包含以下分组：
   - 制造(MFG)：产品(product)、BOM(bom)、工艺路线(routing)
   - 供应链(SCM)：采购(procurement)、库存(inventory)、设施(facility)、发货(shipment)
   - 财务(Finance)：发票(invoice)
   - 销售(Sales)：订单(order)
   - 组织(Organization)：Party(party)
2. THE NavId 类型 SHALL 扩展以包含子系统级别的标识符：'mfg'、'scm'、'finance'、'sales'、'org'
3. THE PanelContext 类型 SHALL 为每个子系统 NavId 定义上下文类型
4. WHEN 新模块需要添加到某子系统时, THE 开发者 SHALL 只需在子系统配置的 children 数组中添加新条目

### 需求 4：导航状态管理

**用户故事：** 作为 ERP 用户，我希望导航状态在子系统和模块之间正确同步，以便侧边栏高亮始终反映当前位置。

#### 验收标准

1. WHEN 用户在某个模块页面时, THE Sidebar SHALL 高亮该模块所属的子系统图标
2. WHEN 路由变化到某个模块路径时, THE Navigation Store SHALL 自动将 activeNavId 设置为该模块所属的子系统 ID
3. WHEN 页面刷新后, THE Navigation Store SHALL 从 localStorage 恢复正确的子系统和模块激活状态
4. WHEN 用户通过浏览器前进/后退导航时, THE Sidebar SHALL 正确更新子系统高亮状态

### 需求 5：图标系统扩展

**用户故事：** 作为开发者，我希望为每个子系统配置合适的图标，以便用户能直观识别各子系统。

#### 验收标准

1. THE icons.js SHALL 导入并导出以下子系统图标：Factory(制造)、Boxes(供应链)、DollarSign(财务)、ShoppingBag(销售)、Building2(组织)
2. THE 每个子系统图标 SHALL 与已有模块图标视觉上有明确区分，避免混淆

### 需求 6：路由兼容性

**用户故事：** 作为 ERP 用户，我希望导航重构不影响现有页面的访问路径，以便书签和直接 URL 访问仍然有效。

#### 验收标准

1. THE 所有现有模块路由 SHALL 保持不变（/party, /product, /order, /facility, /invoice, /shipment, /bom, /routing, /procurement, /inventory）
2. WHEN 用户通过 URL 直接访问某个模块路由时, THE System SHALL 正确显示该模块页面，并将侧边栏高亮到对应的子系统图标
3. THE router/index.js SHALL 不做任何修改
