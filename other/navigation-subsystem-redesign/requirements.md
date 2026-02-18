---
name: navigation-subsystem-redesign
category: other
description: Template for Navigation Subsystem Redesign
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档

## 简介

将当前 ERP 前端的扁平化侧边栏导航（11 个一级图标）重构为基于子系统的分层导航结构。参考 Moqui 原始设计理念，将业务模块按子系统分组，侧边栏仅显示子系统级别图标（6 个），点击子系统后在 ContextPanel 中展示该子系统下的模块列表。此变更纯前端，不涉及后端改动。

## 术语表

- **Sidebar（侧边栏）**: 应用左侧极窄图标栏，显示一级导航入口
- **ContextPanel（上下文面板）**: 侧边栏右侧的中间面板，显示当前导航项的详细内容
- **Subsystem（子系统）**: 一组相关业务模块的逻辑分组，如"制造"包含产品、BOM、工艺路线
- **SubsystemPanel（子系统面板）**: ContextPanel 中用于展示子系统内模块列表的通用组件
- **NavItem（导航项）**: App.vue 中 navItems 数组的数据结构，描述侧边栏按钮
- **NavId**: navigation.ts 中的导航标识符类型，用于标识当前激活的导航项
- **Module（模块）**: 子系统下的具体业务功能，如"产品管理"、"BOM 管理"

## 需求

### 需求 1：子系统分组结构定义

**用户故事：** 作为 ERP 用户，我希望侧边栏按子系统分组显示导航，以便快速定位到目标业务领域。

#### 验收标准

1. THE Sidebar SHALL 显示以下 6 个子系统级导航项：概览（Dashboard）、制造（MFG）、供应链（SCM）、财务（Finance）、销售（Sales）、组织（Organization）
2. THE NavItem 数据结构 SHALL 包含 children 属性，用于定义子系统下的模块列表
3. WHEN 子系统"制造"被定义时，THE NavItem SHALL 包含子模块：产品管理、BOM 管理、工艺路线
4. WHEN 子系统"供应链"被定义时，THE NavItem SHALL 包含子模块：采购管理、库存管理、设施管理、发货管理
5. WHEN 子系统"财务"被定义时，THE NavItem SHALL 包含子模块：发票管理
6. WHEN 子系统"销售"被定义时，THE NavItem SHALL 包含子模块：订单管理
7. WHEN 子系统"组织"被定义时，THE NavItem SHALL 包含子模块：Party 管理
8. THE "概览" NavItem SHALL 作为独立项存在，不包含 children 属性

### 需求 2：侧边栏渲染更新

**用户故事：** 作为 ERP 用户，我希望侧边栏图标数量从 11 个减少到 6 个，以获得更清晰的视觉体验。

#### 验收标准

1. THE Sidebar SHALL 仅渲染子系统级别的图标按钮，不渲染模块级别的图标
2. THE Sidebar SHALL 为每个子系统使用对应的图标：概览用 Home、制造用 Factory、供应链用 Truck、财务用 Receipt、销售用 ShoppingCart、组织用 Users
3. WHEN 用户点击子系统图标时，THE Sidebar SHALL 将该子系统标记为激活状态并显示高亮样式
4. WHEN 用户点击"概览"图标时，THE Sidebar SHALL 直接导航到 Dashboard 页面

### 需求 3：NavId 类型与导航状态扩展

**用户故事：** 作为开发者，我希望 NavId 类型支持子系统标识符，以便导航状态管理能正确区分子系统和模块。

#### 验收标准

1. THE NavId 类型 SHALL 新增子系统标识符：mfg、scm、finance、sales、org
2. THE PanelContext 类型 SHALL 为每个新增的子系统标识符定义对应的上下文类型
3. THE componentMap SHALL 为每个子系统标识符映射到 SubsystemPanel 组件
4. THE DEFAULT_CONTEXTS SHALL 为每个新增的子系统标识符提供默认上下文值

### 需求 4：SubsystemPanel 组件

**用户故事：** 作为 ERP 用户，我希望点击子系统图标后在 ContextPanel 中看到该子系统下的模块列表，以便选择具体模块进入。

#### 验收标准

1. WHEN 一个子系统被激活时，THE SubsystemPanel SHALL 在 ContextPanel 中显示该子系统下所有模块的列表
2. THE SubsystemPanel SHALL 为每个模块项显示图标、名称和简要描述
3. WHEN 用户点击模块列表中的某个模块时，THE SubsystemPanel SHALL 导航到该模块的列表视图路由
4. WHEN 用户点击模块列表中的某个模块时，THE NavigationStore SHALL 将 activeNavId 更新为该模块的 NavId
5. WHEN 一个模块被激活后，THE ContextPanel SHALL 切换显示该模块原有的专属面板组件（如 ProductPanel、BomPanel 等）
6. THE SubsystemPanel SHALL 高亮显示当前激活的模块项

### 需求 5：路由同步与状态一致性

**用户故事：** 作为 ERP 用户，我希望通过 URL 直接访问模块页面时，侧边栏能正确高亮对应的子系统图标。

#### 验收标准

1. WHEN 用户通过 URL 直接访问某个模块路由（如 /product）时，THE Sidebar SHALL 高亮该模块所属子系统的图标
2. WHEN 路由变化时，THE NavigationStore SHALL 根据路由路径自动推断并激活对应的子系统或模块
3. WHEN 用户从模块页面点击所属子系统图标时，THE ContextPanel SHALL 显示 SubsystemPanel 而非模块专属面板
4. THE NavigationStore SHALL 维护模块到子系统的映射关系，用于路由同步

### 需求 6：图标系统扩展

**用户故事：** 作为开发者，我希望图标系统支持新增的子系统图标，以便侧边栏正确渲染。

#### 验收标准

1. THE icons.js SHALL 从 lucide-vue-next 导入 Factory 图标组件
2. THE icons 导出对象 SHALL 包含 factory 属性映射到 Factory 图标组件
3. IF Factory 图标在 lucide-vue-next 中不可用，THEN THE icons.js SHALL 使用 Cog 或其他合适的替代图标

### 需求 7：向后兼容与平滑过渡

**用户故事：** 作为 ERP 用户，我希望导航重构后所有现有模块的路由、视图和面板功能保持不变。

#### 验收标准

1. THE Router 配置 SHALL 保持所有现有模块路由不变
2. THE 各模块专属 Panel 组件（PartyPanel、ProductPanel 等）SHALL 保持功能不变
3. WHEN 用户从旧版 localStorage 导航状态恢复时，THE NavigationStore SHALL 兼容处理旧的 NavId 值
4. THE 系统管理（Admin）导航项 SHALL 保持现有的 children 结构和行为不变
