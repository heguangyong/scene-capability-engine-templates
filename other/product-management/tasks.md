---
name: product-management
category: other
description: Template for Product Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实施计划：产品管理模块（{{SPEC_NAME_TITLE}}）

## 概述

按照 Party 管理模块的架构模式，分层实现产品管理模块：后端 Service → Controller → 前端 Types → API Service → Store → 页面组件 → 导航集成。每一步构建在前一步之上，确保无孤立代码。

## 任务

- [x] 1. 后端 ProductService 实现
  - [x] 1.1 创建 ProductService.java，实现 listProducts（分页、搜索、按类型/状态筛选）和 getProductDetail（含价格、分类、标识码）方法
    - 遵循 PartyService 模式：ExecutionContextFactory 注入、authz 管理、errorResult 辅助方法
    - 实体查询：mantle.product.Product、mantle.product.ProductPrice、mantle.product.category.ProductCategoryMember、mantle.product.ProductIdentification
    - 详情查询仅返回有效记录（thruDate 为空或晚于当前时间）
    - _Requirements: 1.1-1.5, 2.1-2.4_
  - [x] 1.2 实现 createProduct 和 updateProduct 方法
    - createProduct：验证 productName 和 productTypeEnumId 必填，创建 Product 实体，初始 statusId 设为 ProdActive
    - updateProduct：验证 productId 存在，支持更新 productName、description、comments、salesIntroductionDate、salesDiscontinuationDate、amountUomId
    - _Requirements: 3.1-3.4, 4.1-4.3_
  - [x] 1.3 实现 toggleStatus、价格管理（addPrice/updatePrice/deletePrice）、分类管理（listCategories/addCategory/removeCategory）、标识码管理（addIdentification/updateIdentification/deleteIdentification）方法
    - toggleStatus：ProdActive ↔ ProdInactive 切换
    - deletePrice/removeCategory：软删除（设置 thruDate）
    - deleteIdentification：硬删除
    - addIdentification：检查重复 productIdTypeEnumId，重复返回 DUPLICATE_ERROR
    - _Requirements: 5.1-5.3, 6.1-6.4, 7.1-7.4, 8.1-8.5_
  - [x]* 1.4 编写 ProductService 单元测试
    - 覆盖所有 CRUD 操作、验证逻辑、错误处理、边界情况
    - _Requirements: 1.1-8.5_

- [x] 2. 后端 ProductController 实现
  - [x] 2.1 创建 ProductController.java，注册所有 REST 端点并调用 ProductService
    - 遵循 PartyController 模式：路由注册、参数解析、错误码映射
    - 静态路径（/categories）在参数化路径（/{productId}）之前注册
    - 参数验证：必填字段缺失返回 400
    - _Requirements: 9.1-9.10_
  - [x] 2.2 在应用启动类中注册 ProductController（参考 PartyController 的注册方式）
    - _Requirements: 9.1_
  - [x]* 2.3 编写 ProductController 单元测试
    - 验证路由注册、参数验证、错误映射
    - **Property 11: 前端表单验证——缺少产品名称**
    - **Validates: Requirements 9.10, 11.3**

- [x] 3. 检查点 - 后端编译和测试
  - 确保 `mvn clean package` 编译通过，所有测试通过，如有问题请询问用户。

- [x] 4. 前端类型定义和 API 服务
  - [x] 4.1 创建 frontend/src/renderer/types/product.ts，定义所有 TypeScript 接口
    - ProductListItem、ProductDetail、ProductPriceItem、ProductCategoryMemberItem、ProductIdentificationItem
    - ProductCategory、ProductListParams、CreateProductRequest、UpdateProductRequest、PriceRequest、IdentificationRequest
    - PaginationMeta（复用或定义）
    - _Requirements: 2.1, 13.1_
  - [x] 4.2 创建 frontend/src/renderer/services/productApiService.ts，封装所有 REST API 调用
    - 遵循 partyApiService 模式：使用 apiV1Client，路径前缀 /product
    - 覆盖：列表、详情、创建、更新、状态切换、价格 CRUD、分类管理、标识码管理、分类列表
    - _Requirements: 9.1-9.9_

- [x] 5. 前端 Store 实现
  - [x] 5.1 创建 frontend/src/renderer/stores/product.ts，实现 productStore
    - 遵循 partyStore 模式：state、getters（totalPages、hasNextPage、hasPreviousPage）、actions
    - 导出纯函数：filterProductsByKeyword、validateCreateProductForm
    - actions：fetchProducts、searchProducts、setPage、setFilter、fetchProductDetail、createProduct、updateProduct、toggleStatus
    - actions：addPrice、updatePrice、deletePrice、fetchCategories、addCategory、removeCategory、addIdentification、updateIdentification、deleteIdentification
    - _Requirements: 13.1-13.4_
  - [x]* 5.2 编写 productStore 纯函数属性测试（fast-check）
    - **Property 1: 筛选结果正确性** — filterProductsByKeyword 返回的所有产品名称包含关键词
    - **Validates: Requirements 1.2, 1.3, 1.4**
    - **Property 11: 前端表单验证** — validateCreateProductForm 对缺少 productName 的请求返回错误
    - **Validates: Requirements 11.3**
    - **Property 12: 搜索/筛选重置分页** — 搜索或筛选操作后 currentPageIndex 为 0
    - **Validates: Requirements 13.3**

- [x] 6. 前端页面组件
  - [x] 6.1 创建 ProductListView.vue — 产品列表页
    - 遵循 PartyListView 模式：表格（productId、产品名称、产品类型、状态）、搜索框（300ms 防抖）、类型/状态筛选、分页、创建按钮
    - 产品类型 badge：PtAsset（实物商品）、PtDigital（数字商品）、PtService（服务）
    - 状态 badge：ProdActive（活跃）、ProdInactive（停用）
    - 点击行导航到 /product/:productId
    - _Requirements: 10.1-10.6_
  - [x] 6.2 创建 ProductFormDialog.vue — 创建产品对话框
    - 遵循 PartyFormDialog 模式：产品类型选择、产品名称、描述输入
    - 表单验证：productName 必填
    - 提交中禁用按钮、显示加载状态
    - _Requirements: 11.1-11.4_
  - [x] 6.3 创建 ProductDetailView.vue — 产品详情页（Tab 布局）
    - Tab 1 基本信息：显示/编辑 productName、description、comments、salesIntroductionDate、salesDiscontinuationDate、amountUomId、状态切换按钮
    - Tab 2 价格：价格列表表格、添加/删除价格
    - Tab 3 分类：已分配分类列表、分配/移除分类
    - Tab 4 标识码：标识码列表、添加/编辑/删除标识码
    - 加载失败显示错误信息和重试按钮
    - _Requirements: 12.1-12.7_

- [x] 7. 导航集成
  - [x] 7.1 创建 ProductPanel.vue 二级导航面板，遵循 PartyPanel 模式
    - _Requirements: 14.4_
  - [x] 7.2 在 router/index.js 添加产品路由（/product、/product/:productId）
    - _Requirements: 14.1, 14.2_
  - [x] 7.3 在 App.vue navItems 添加"产品管理"导航项（icon: icons.package, route: /product）
    - _Requirements: 14.3_
  - [x] 7.4 在 ContextPanel.vue 注册 ProductPanel 到 panelComponentMap
    - _Requirements: 14.4_

- [x] 8. 最终检查点 - 全量测试
  - 确保后端 `mvn test` 和前端 `npm test` 全部通过，如有问题请询问用户。

## 备注

- 标记 `*` 的任务为可选，可跳过以加速 MVP 交付
- 每个任务引用具体需求以确保可追溯性
- 检查点确保增量验证
- 属性测试验证通用正确性属性，单元测试验证具体示例和边界情况
