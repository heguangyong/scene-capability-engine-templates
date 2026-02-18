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

# 需求文档

## 简介

{{SPEC_NAME_TITLE}} 是前端 Screen 探索模块，用于浏览、查看和分析 Moqui ERP 系统中的 XML Screen 定义。该模块利用已有的后端 Screen REST API（ScreenController / ScreenCatalogService / ScreenService），在 Vue 3 前端中提供结构化的 Screen 浏览、详情查看、表单检查和 Widget 树可视化功能。这是 XML Screen → Vue 3 模板转换的前置探索阶段。

## 术语表

- **Screen_Explorer**: 前端 Screen 探索模块，提供 Screen 浏览、详情查看和分析功能
- **Screen_Catalog_Browser**: Screen 目录浏览器组件，展示可用 Screen 列表并支持分页、搜索和组件筛选
- **Screen_Detail_Viewer**: Screen 详情查看器组件，展示 Screen 定义的结构化信息（参数、转换、表单、Widget）
- **Form_Inspector**: 表单检查器组件，展示表单字段定义、类型和验证规则
- **Widget_Tree_Viewer**: Widget 树查看器组件，以树形结构可视化 Screen 的 Widget 层次
- **Screen_API_Service**: 前端 API 服务层，封装对后端 Screen REST 端点的调用
- **Screen_Store**: Pinia 状态管理 Store，管理 Screen 数据的获取、缓存和状态
- **Catalog_Path**: Screen 在目录中的唯一路径标识，格式为 `component/relative/path`
- **Screen_Summary**: Screen 摘要信息，包含组件名、文件名、相对路径和目录路径
- **Screen_Definition**: Screen 完整定义，包含参数、转换、动作和 Widget 结构

## 需求

### 需求 1: Screen API 服务层

**用户故事:** 作为前端开发者，我希望有一个封装好的 API 服务层来调用后端 Screen REST 端点，以便前端组件能统一获取 Screen 数据。

#### 验收标准

1. THE Screen_API_Service SHALL 提供 `listScreens(component?, keyword?, pageIndex?, pageSize?)` 方法，调用 `GET /api/v1/screens` 端点并返回类型化的 Screen 列表结果
2. THE Screen_API_Service SHALL 提供 `getScreenDefinition(catalogPath)` 方法，调用 `GET /api/v1/screens/definition?path=X` 端点并返回类型化的 Screen 定义结果
3. THE Screen_API_Service SHALL 提供 `getScreenForms(catalogPath)` 方法，调用 `GET /api/v1/screens/forms?path=X` 端点并返回类型化的表单数据结果
4. THE Screen_API_Service SHALL 提供 `getScreenWidgets(catalogPath)` 方法，调用 `GET /api/v1/screens/widgets?path=X` 端点并返回类型化的 Widget 数据结果
5. IF Screen_API_Service 调用返回错误响应, THEN THE Screen_API_Service SHALL 将错误转换为统一的 ApiV1Result 错误格式并传递给调用方
6. THE Screen_API_Service SHALL 复用现有的 apiV1Client 实例进行 HTTP 请求，遵循项目已有的认证拦截器和错误处理模式

### 需求 2: Screen 数据状态管理

**用户故事:** 作为前端开发者，我希望有一个 Pinia Store 来管理 Screen 数据的获取和缓存，以便组件能响应式地访问 Screen 数据并避免重复请求。

#### 验收标准

1. THE Screen_Store SHALL 管理 Screen 列表数据，包含当前页数据、分页信息、加载状态和错误状态
2. THE Screen_Store SHALL 管理当前选中 Screen 的详情数据，包含定义、表单和 Widget 信息
3. WHEN Screen_Store 获取 Screen 列表时, THE Screen_Store SHALL 更新加载状态为 true，请求完成后更新为 false
4. WHEN Screen_Store 获取数据失败时, THE Screen_Store SHALL 将错误信息存储到错误状态中，供组件展示
5. THE Screen_Store SHALL 提供 `fetchScreens` action 来获取 Screen 列表，支持组件筛选、关键词搜索和分页参数
6. THE Screen_Store SHALL 提供 `fetchScreenDetail` action 来获取指定 Screen 的完整定义
7. THE Screen_Store SHALL 提供 `fetchScreenForms` action 来获取指定 Screen 的表单数据
8. THE Screen_Store SHALL 提供 `fetchScreenWidgets` action 来获取指定 Screen 的 Widget 数据

### 需求 3: Screen 目录浏览器

**用户故事:** 作为 ERP 管理员，我希望能浏览所有可用的 Moqui Screen，按组件分类查看并搜索特定 Screen，以便了解系统中有哪些 Screen 可用。

#### 验收标准

1. WHEN 用户进入 Screen Explorer 页面时, THE Screen_Catalog_Browser SHALL 加载并展示 Screen 列表，每个条目显示组件名、文件名和相对路径
2. WHEN 用户在搜索框中输入关键词时, THE Screen_Catalog_Browser SHALL 使用该关键词过滤 Screen 列表，仅显示匹配的结果
3. WHEN 用户选择组件筛选器中的某个组件时, THE Screen_Catalog_Browser SHALL 仅显示属于该组件的 Screen
4. WHEN Screen 列表超过单页显示数量时, THE Screen_Catalog_Browser SHALL 提供分页控件，允许用户在页面间导航
5. WHEN 用户点击某个 Screen 条目时, THE Screen_Catalog_Browser SHALL 导航到该 Screen 的详情页面
6. WHILE Screen 列表正在加载时, THE Screen_Catalog_Browser SHALL 显示加载指示器
7. IF Screen 列表加载失败, THEN THE Screen_Catalog_Browser SHALL 显示错误信息和重试按钮

### 需求 4: Screen 详情查看器

**用户故事:** 作为 ERP 管理员，我希望能查看某个 Screen 的完整定义信息，包括参数、转换和 Widget 结构，以便理解该 Screen 的功能和结构。

#### 验收标准

1. WHEN 用户打开某个 Screen 的详情页面时, THE Screen_Detail_Viewer SHALL 加载并展示该 Screen 的完整定义信息
2. THE Screen_Detail_Viewer SHALL 以结构化方式展示 Screen 的参数列表，每个参数显示名称和是否必填
3. THE Screen_Detail_Viewer SHALL 以结构化方式展示 Screen 的转换（transition）列表，每个转换显示名称和关联的服务调用
4. THE Screen_Detail_Viewer SHALL 提供标签页或折叠面板，分别展示参数、转换、表单和 Widget 信息
5. WHILE Screen 详情正在加载时, THE Screen_Detail_Viewer SHALL 显示加载指示器
6. IF Screen 详情加载失败, THEN THE Screen_Detail_Viewer SHALL 显示错误信息和返回列表的链接

### 需求 5: 表单检查器

**用户故事:** 作为 ERP 管理员，我希望能检查 Screen 中的表单定义，查看字段名称、类型和验证规则，以便理解表单的数据结构。

#### 验收标准

1. WHEN 用户查看某个 Screen 的表单信息时, THE Form_Inspector SHALL 加载并展示该 Screen 中所有表单的列表
2. THE Form_Inspector SHALL 对每个表单展示其字段列表，每个字段显示名称和类型信息
3. WHEN 用户点击某个表单时, THE Form_Inspector SHALL 展开该表单的详细字段信息
4. IF Screen 不包含任何表单, THEN THE Form_Inspector SHALL 显示"该 Screen 无表单定义"的提示信息

### 需求 6: Widget 树查看器

**用户故事:** 作为 ERP 管理员，我希望能以树形结构查看 Screen 的 Widget 层次，以便理解 Screen 的 UI 布局结构。

#### 验收标准

1. WHEN 用户查看某个 Screen 的 Widget 信息时, THE Widget_Tree_Viewer SHALL 加载并以树形结构展示 Widget 层次
2. THE Widget_Tree_Viewer SHALL 对每个 Widget 节点显示其类型名称和关键属性
3. WHEN 用户点击某个 Widget 节点时, THE Widget_Tree_Viewer SHALL 展开或折叠该节点的子 Widget
4. THE Widget_Tree_Viewer SHALL 使用缩进和连接线来清晰表示 Widget 的父子关系
5. IF Screen 不包含任何 Widget, THEN THE Widget_Tree_Viewer SHALL 显示"该 Screen 无 Widget 定义"的提示信息

### 需求 7: 路由和导航集成

**用户故事:** 作为用户，我希望能通过侧边栏导航进入 Screen Explorer 模块，并在模块内部通过路由在列表和详情之间切换。

#### 验收标准

1. THE Screen_Explorer SHALL 注册 `/screens` 路由，作为 Screen 目录浏览器的入口
2. THE Screen_Explorer SHALL 注册 `/screens/:catalogPath` 路由，作为 Screen 详情查看器的入口
3. THE Screen_Explorer SHALL 在侧边栏导航中添加"Screen 探索"导航项，点击后导航到 `/screens` 路由
4. WHEN 用户在 Screen 详情页面点击返回时, THE Screen_Explorer SHALL 导航回 Screen 列表页面并保留之前的搜索和分页状态
5. THE Screen_Explorer SHALL 遵循现有的路由守卫规则，要求用户已认证才能访问

### 需求 8: TypeScript 类型定义

**用户故事:** 作为前端开发者，我希望有完整的 TypeScript 类型定义来描述 Screen 相关的数据结构，以便获得类型安全和 IDE 智能提示。

#### 验收标准

1. THE Screen_Explorer SHALL 定义 `ScreenSummary` 类型，包含 component、fileName、relativePath 和 catalogPath 字段
2. THE Screen_Explorer SHALL 定义 `ScreenDefinition` 类型，包含 screenName、parameters、transitions 和 widgets 字段
3. THE Screen_Explorer SHALL 定义 `ScreenForm` 类型，包含 formName、formType 和 fields 字段
4. THE Screen_Explorer SHALL 定义 `ScreenWidget` 类型，包含 type、attributes 和 children 字段
5. THE Screen_Explorer SHALL 定义 `ScreenListResponse` 类型，匹配后端 `/api/v1/screens` 端点的响应格式
6. THE Screen_Explorer SHALL 将所有 Screen 相关类型定义导出，供 API 服务层、Store 和组件使用
