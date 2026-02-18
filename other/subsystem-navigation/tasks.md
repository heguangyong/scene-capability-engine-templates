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

﻿# 实施计划：子系统导航架构重构

## 概述

将侧边栏从扁平 11 图标改为 7 个子系统分组的二级导航。改动文件：icons.js、navigation.ts、App.vue、ContextPanel.vue，新增 SubsystemPanel.vue。路由和后端不做任何改动。

## 任务

- [x] 1. 扩展图标系统
  - [x] 1.1 在 `frontend/src/renderer/utils/icons.js` 中导入子系统图标
    - 从 lucide-vue-next 导入 Factory, Boxes, DollarSign, ShoppingBag, Building2
    - 在 icons 对象中添加 factory, boxes, dollarSign, shoppingBag, building2
    - 如果 Boxes 不存在于 lucide-vue-next，使用 PackageOpen 或 Container 作为备选
    - _Requirements: 5.1, 5.2_

- [x] 2. 重构导航状态管理
  - [x] 2.1 扩展 `frontend/src/renderer/stores/navigation.ts` 的类型定义
    - 新增 SubsystemId 类型: 'mfg' | 'scm' | 'finance' | 'sales' | 'org'
    - 新增 ModuleId 类型: 'party' | 'product' | 'order' | 'facility' | 'invoice' | 'shipment' | 'bom' | 'routing' | 'procurement' | 'inventory'
    - 扩展 NavId 类型包含 SubsystemId
    - 新增 SUBSYSTEM_MODULE_MAP: Record<SubsystemId, ModuleId[]> 常量，定义子系统到模块的映射
    - 新增 MODULE_SUBSYSTEM_MAP: Record<ModuleId, SubsystemId> 常量，定义模块到子系统的反向映射
    - 扩展 PanelContext 类型，为 mfg/scm/finance/sales/org 添加空对象类型
    - 扩展 DEFAULT_CONTEXTS 添加 mfg/scm/finance/sales/org 的默认值
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 2.2 扩展 NavigationState 和 store actions
    - 在 NavigationState 中新增 activeSubsystemId: SubsystemId | null
    - 修改 setActiveNav action：当设置模块 ID 时，自动通过 MODULE_SUBSYSTEM_MAP 更新 activeSubsystemId
    - 修改 persistNavigationState：保存 activeSubsystemId
    - 修改 initializeFromStorage：恢复 activeSubsystemId
    - 扩展 componentMap getter 添加子系统 ID 到 'SubsystemPanel' 的映射
    - 新增 getSubsystemForModule(moduleId) 辅助方法
    - 新增 navigateToModule(moduleId) action：设置 activeNavId 为模块 ID，activeSubsystemId 为对应子系统
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [x] 2.3 编写 navigation.ts 的属性测试（可选）
    - **Property 4: 模块路由到子系统高亮映射**
    - **Validates: Requirements 4.1, 4.2, 6.2**
    - 使用 fast-check 生成随机 ModuleId，验证 MODULE_SUBSYSTEM_MAP 返回正确的 SubsystemId
    - **Property 5: 导航状态持久化往返**
    - **Validates: Requirements 4.3**
    - 使用 fast-check 生成随机 NavId + SubsystemId，序列化到 JSON 后反序列化，验证等价

- [x] 3. 创建 SubsystemPanel 组件
  - [x] 3.1 创建 `frontend/src/renderer/components/panels/SubsystemPanel.vue`
    - Props: title (string), modules (Array<{id, route, label, icon, description}>)
    - 渲染模块列表，每个模块显示图标、名称、描述
    - 点击模块时：调用 navigationStore.navigateToModule(mod.id)，然后 router.push(mod.route)
    - 空模块列表时显示"暂无模块"提示
    - 样式与现有面板风格一致（使用 .section, .section-item 等已有 CSS 类名风格）
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 3.2 编写 SubsystemPanel 的属性测试（可选）
    - **Property 3: SubsystemPanel 渲染完整性**
    - **Validates: Requirements 2.1, 2.3**
    - 使用 fast-check 生成随机模块配置列表，挂载 SubsystemPanel，验证渲染输出包含每个模块的名称和描述

- [x] 4. Checkpoint - 确保 navigation.ts 和 SubsystemPanel 编译通过
  - 运行 `npm run build` 确保无编译错误
  - 确保所有测试通过，如有问题请询问用户

- [x] 5. 重构 App.vue 导航结构
  - [x] 5.1 重构 `frontend/src/renderer/App.vue` 的 navItems 数据
    - 将扁平 navItems 数组改为嵌套结构（7 个一级项）
    - 每个子系统项包含 type: 'subsystem' 和 children 数组
    - dashboard 和 admin 项包含 type: 'direct' 和 route
    - 子系统分组：MFG(product,bom,routing), SCM(procurement,inventory,facility,shipment), Finance(invoice), Sales(order), Org(party)
    - _Requirements: 1.1, 1.5, 3.1_

  - [x] 5.2 更新 handleNavClick 函数
    - type === 'direct' 时：调用 navigationStore.setActiveNav + router.push
    - type === 'subsystem' 时：只调用 navigationStore.setActiveNav（不导航路由）
    - _Requirements: 1.2, 1.3, 1.4_

  - [x] 5.3 更新侧边栏高亮逻辑
    - 将 activeNavId 的高亮判断改为使用 activeSubsystemId
    - 对于 dashboard/admin/settings：直接比较 activeNavId
    - 对于子系统：比较 activeSubsystemId 或 activeNavId（当 activeNavId 是子系统 ID 时）
    - 计算属性 sidebarActiveId：返回当前应高亮的侧边栏项 ID
    - _Requirements: 4.1, 4.4_

  - [x] 5.4 更新路由监听 watch
    - 路由变化时，通过 MODULE_SUBSYSTEM_MAP 查找模块所属子系统
    - 如果匹配到模块路由：设置 activeNavId 为模块 ID（ContextPanel 显示模块面板），activeSubsystemId 为子系统 ID（侧边栏高亮）
    - 如果是 dashboard/admin/settings 路由：直接设置 activeNavId
    - 导入 MODULE_SUBSYSTEM_MAP 从 navigation.ts
    - _Requirements: 4.2, 6.2_

- [x] 6. 更新 ContextPanel 面板映射
  - [x] 6.1 修改 `frontend/src/renderer/components/panels/ContextPanel.vue`
    - 导入 SubsystemPanel 组件
    - 在 panelComponentMap 中添加 mfg/scm/finance/sales/org → SubsystemPanel 的映射
    - 更新 panelProps 计算属性：当 activeNavId 是子系统 ID 时，传递 title 和 modules props
    - 从 App.vue 的 navItems 或 navigation.ts 的 SUBSYSTEM_MODULE_MAP 获取子系统配置
    - 需要在 ContextPanel 中定义子系统配置数据（包含 title、modules 及其 icon 引用）
    - _Requirements: 1.4, 2.1_

- [x] 7. Final Checkpoint - 全量验证
  - 运行 `npm run build` 确保编译通过
  - 确保所有测试通过，如有问题请询问用户
  - 验证以下场景：
    - 侧边栏显示 7 个一级图标
    - 点击子系统图标 → ContextPanel 显示模块列表
    - 点击模块 → 导航到正确路由 + 面板切换
    - 侧边栏高亮正确反映当前子系统
    - 直接 URL 访问模块路由正常工作
    - 页面刷新后状态恢复正确

## 备注

- 标记 `*` 的任务为可选测试任务，可跳过以加速 MVP
- 路由文件 router/index.js 不做任何修改
- 后端不做任何修改，纯前端重构
- 所有现有模块路由保持不变
- Electron 环境下可能需要 Ctrl+Shift+R 清除 localStorage 缓存才能看到新导航
- lucide-vue-next 中 Boxes 图标如不存在，备选方案：PackageOpen、Container、Combine

