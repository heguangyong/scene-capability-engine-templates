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

# 需求文档：产品管理模块（{{SPEC_NAME_TITLE}}）

## 简介

产品管理是 Moqui ERP 的核心基础数据模块，负责产品的全生命周期管理，包括产品基本信息、价格、分类、标识码等。后续的订单、库存、发货模块均依赖产品定义。本模块遵循 Party 管理模块的架构模式，提供后端 REST API 和前端 Vue 页面。

## 术语表

- **Product_Service**: 后端产品管理服务层，负责产品数据的 CRUD 操作和业务逻辑
- **Product_Controller**: 后端 REST 控制器，处理 HTTP 请求并调用 Product_Service
- **Product_Store**: 前端 Pinia Store，管理产品相关的状态和异步操作
- **Product_API_Service**: 前端 API 服务层，封装对后端 REST API 的调用
- **Product_List_View**: 前端产品列表页面组件
- **Product_Detail_View**: 前端产品详情页面组件（Tab 布局）
- **Product_Form_Dialog**: 前端创建产品对话框组件
- **Product**: 产品实体，包含 productId、productName、productTypeEnumId、statusId 等核心字段
- **Product_Category**: 产品分类实体，支持层级结构
- **Product_Price**: 产品价格实体，支持多种价格类型和有效期
- **Product_Identification**: 产品标识码实体，如条码、SKU 等
- **Product_Type**: 产品类型枚举，包括 PtAsset（实物商品）、PtDigital（数字商品）、PtService（服务）等

## 需求

### 需求 1：产品列表查询

**用户故事：** 作为 ERP 操作员，我希望能够查看产品列表并进行搜索和筛选，以便快速找到目标产品。

#### 验收标准

1. WHEN 用户请求产品列表, THE Product_Service SHALL 返回分页的产品列表，包含 productId、pseudoId、productName、productTypeEnumId、statusId 和 amountUomId 字段
2. WHEN 用户提供搜索关键词, THE Product_Service SHALL 按产品名称进行模糊匹配并返回匹配结果
3. WHEN 用户指定产品类型筛选条件, THE Product_Service SHALL 仅返回匹配该 productTypeEnumId 的产品
4. WHEN 用户指定产品状态筛选条件, THE Product_Service SHALL 仅返回匹配该 statusId 的产品
5. WHEN 产品列表结果超过单页容量, THE Product_Service SHALL 返回分页元数据（pageIndex、pageSize、totalCount、totalPages）

### 需求 2：产品详情查询

**用户故事：** 作为 ERP 操作员，我希望查看产品的完整详情，以便了解产品的所有属性和关联数据。

#### 验收标准

1. WHEN 用户请求指定 productId 的产品详情, THE Product_Service SHALL 返回产品基本信息、价格列表、分类列表和标识码列表
2. IF 请求的 productId 不存在, THEN THE Product_Service SHALL 返回 RESOURCE_NOT_FOUND 错误
3. WHEN 返回价格列表, THE Product_Service SHALL 仅包含当前有效的价格记录（thruDate 为空或晚于当前时间）
4. WHEN 返回分类列表, THE Product_Service SHALL 仅包含当前有效的分类关联（thruDate 为空或晚于当前时间）

### 需求 3：创建产品

**用户故事：** 作为 ERP 操作员，我希望能够创建新产品，以便将新商品录入系统。

#### 验收标准

1. WHEN 用户提交包含 productName 和 productTypeEnumId 的创建请求, THE Product_Service SHALL 创建新产品并返回生成的 productId
2. IF 创建请求缺少 productName, THEN THE Product_Service SHALL 返回 VALIDATION_ERROR 错误
3. IF 创建请求缺少 productTypeEnumId, THEN THE Product_Service SHALL 返回 VALIDATION_ERROR 错误
4. WHEN 产品创建成功, THE Product_Service SHALL 将产品初始 statusId 设置为 ProdActive

### 需求 4：更新产品基本信息

**用户故事：** 作为 ERP 操作员，我希望能够修改产品的基本信息，以便保持产品数据的准确性。

#### 验收标准

1. WHEN 用户提交产品更新请求, THE Product_Service SHALL 更新指定 productId 的产品字段并返回成功结果
2. IF 更新请求的 productId 不存在, THEN THE Product_Service SHALL 返回 RESOURCE_NOT_FOUND 错误
3. THE Product_Service SHALL 支持更新 productName、description、comments、salesIntroductionDate、salesDiscontinuationDate、amountUomId 字段

### 需求 5：产品状态管理

**用户故事：** 作为 ERP 操作员，我希望能够管理产品的上下架状态，以便控制产品的可用性。

#### 验收标准

1. WHEN 用户对 ProdActive 状态的产品执行停用操作, THE Product_Service SHALL 将 statusId 更新为 ProdInactive
2. WHEN 用户对 ProdInactive 状态的产品执行激活操作, THE Product_Service SHALL 将 statusId 更新为 ProdActive
3. IF 状态切换请求的 productId 不存在, THEN THE Product_Service SHALL 返回 RESOURCE_NOT_FOUND 错误

### 需求 6：产品价格管理

**用户故事：** 作为 ERP 操作员，我希望能够管理产品的价格信息，以便维护准确的定价数据。

#### 验收标准

1. WHEN 用户为产品添加价格, THE Product_Service SHALL 创建包含 priceTypeEnumId、price、priceUomId 和 fromDate 的价格记录
2. WHEN 用户更新产品价格, THE Product_Service SHALL 修改指定 productPriceId 的价格记录
3. WHEN 用户删除产品价格, THE Product_Service SHALL 通过设置 thruDate 为当前时间来软删除价格记录
4. IF 添加价格时缺少 price 或 priceUomId, THEN THE Product_Service SHALL 返回 VALIDATION_ERROR 错误

### 需求 7：产品分类管理

**用户故事：** 作为 ERP 操作员，我希望能够将产品分配到分类中，以便组织和管理产品目录。

#### 验收标准

1. WHEN 用户将产品分配到分类, THE Product_Service SHALL 创建 ProductCategoryMember 关联记录
2. WHEN 用户从分类中移除产品, THE Product_Service SHALL 通过设置 thruDate 为当前时间来软删除关联记录
3. IF 分配请求的 productCategoryId 不存在, THEN THE Product_Service SHALL 返回 RESOURCE_NOT_FOUND 错误
4. WHEN 查询可用分类列表, THE Product_Service SHALL 返回所有 ProductCategory 记录及其层级关系

### 需求 8：产品标识码管理

**用户故事：** 作为 ERP 操作员，我希望能够管理产品的标识码（条码、SKU 等），以便支持多种产品识别方式。

#### 验收标准

1. WHEN 用户为产品添加标识码, THE Product_Service SHALL 创建包含 productIdTypeEnumId 和 idValue 的标识记录
2. WHEN 用户更新产品标识码, THE Product_Service SHALL 修改指定标识记录的 idValue
3. WHEN 用户删除产品标识码, THE Product_Service SHALL 移除指定的标识记录
4. IF 添加标识码时缺少 productIdTypeEnumId 或 idValue, THEN THE Product_Service SHALL 返回 VALIDATION_ERROR 错误
5. IF 同一产品已存在相同 productIdTypeEnumId 的标识码, THEN THE Product_Service SHALL 返回 DUPLICATE_ERROR 错误

### 需求 9：REST API 端点

**用户故事：** 作为前端开发者，我希望有一组规范的 REST API 端点，以便前端页面能够调用后端服务。

#### 验收标准

1. THE Product_Controller SHALL 在 /api/v1/product 路径下注册所有产品管理端点
2. WHEN 收到 GET /api/v1/product 请求, THE Product_Controller SHALL 调用 Product_Service 的列表查询方法
3. WHEN 收到 GET /api/v1/product/{productId} 请求, THE Product_Controller SHALL 调用 Product_Service 的详情查询方法
4. WHEN 收到 POST /api/v1/product 请求, THE Product_Controller SHALL 验证请求体并调用 Product_Service 的创建方法
5. WHEN 收到 PUT /api/v1/product/{productId} 请求, THE Product_Controller SHALL 调用 Product_Service 的更新方法
6. WHEN 收到 POST /api/v1/product/{productId}/toggle-status 请求, THE Product_Controller SHALL 调用 Product_Service 的状态切换方法
7. WHEN 收到价格管理请求（POST/PUT/DELETE /api/v1/product/{productId}/prices/*）, THE Product_Controller SHALL 调用 Product_Service 的价格管理方法
8. WHEN 收到分类管理请求（POST/DELETE /api/v1/product/{productId}/categories/*）, THE Product_Controller SHALL 调用 Product_Service 的分类管理方法
9. WHEN 收到标识码管理请求（POST/PUT/DELETE /api/v1/product/{productId}/identifications/*）, THE Product_Controller SHALL 调用 Product_Service 的标识码管理方法
10. IF 请求参数验证失败, THEN THE Product_Controller SHALL 返回 HTTP 400 状态码和 VALIDATION_ERROR 错误信息

### 需求 10：前端产品列表页面

**用户故事：** 作为 ERP 操作员，我希望有一个直观的产品列表页面，以便浏览、搜索和管理产品。

#### 验收标准

1. WHEN 用户访问产品列表页面, THE Product_List_View SHALL 显示产品表格，包含 productId、产品名称、产品类型、状态列
2. WHEN 用户在搜索框输入关键词, THE Product_List_View SHALL 在 300ms 防抖后触发搜索请求
3. WHEN 用户选择产品类型或状态筛选条件, THE Product_List_View SHALL 重新加载匹配的产品列表
4. WHEN 用户点击产品行, THE Product_List_View SHALL 导航到该产品的详情页面
5. WHEN 用户点击"创建产品"按钮, THE Product_List_View SHALL 显示 Product_Form_Dialog
6. WHEN 产品列表有多页, THE Product_List_View SHALL 显示分页控件

### 需求 11：前端创建产品对话框

**用户故事：** 作为 ERP 操作员，我希望通过对话框快速创建新产品，以便高效录入产品数据。

#### 验收标准

1. WHEN 对话框打开, THE Product_Form_Dialog SHALL 显示产品类型选择、产品名称和描述输入字段
2. WHEN 用户提交有效的创建表单, THE Product_Form_Dialog SHALL 调用 Product_Store 的创建方法并在成功后关闭对话框
3. IF 用户提交的表单缺少产品名称, THEN THE Product_Form_Dialog SHALL 显示验证错误提示
4. WHEN 创建操作正在进行, THE Product_Form_Dialog SHALL 禁用提交按钮并显示加载状态

### 需求 12：前端产品详情页面

**用户故事：** 作为 ERP 操作员，我希望在详情页面查看和编辑产品的所有信息，以便全面管理产品数据。

#### 验收标准

1. WHEN 用户访问产品详情页面, THE Product_Detail_View SHALL 以 Tab 布局显示基本信息、价格、分类和标识码四个标签页
2. WHEN 用户切换 Tab, THE Product_Detail_View SHALL 显示对应标签页的内容
3. WHEN 用户在基本信息 Tab 编辑字段并保存, THE Product_Detail_View SHALL 调用 Product_Store 的更新方法
4. WHEN 用户在价格 Tab 添加或删除价格, THE Product_Detail_View SHALL 调用 Product_Store 的价格管理方法并刷新价格列表
5. WHEN 用户在分类 Tab 分配或移除分类, THE Product_Detail_View SHALL 调用 Product_Store 的分类管理方法并刷新分类列表
6. WHEN 用户在标识码 Tab 添加、编辑或删除标识码, THE Product_Detail_View SHALL 调用 Product_Store 的标识码管理方法并刷新标识码列表
7. IF 产品详情加载失败, THEN THE Product_Detail_View SHALL 显示错误信息和重试按钮

### 需求 13：前端状态管理

**用户故事：** 作为前端开发者，我希望有统一的状态管理，以便各组件共享产品数据和操作状态。

#### 验收标准

1. THE Product_Store SHALL 管理产品列表、分页信息、加载状态、错误信息和当前产品详情
2. WHEN Product_Store 执行异步操作, THE Product_Store SHALL 正确维护 loading 和 error 状态
3. WHEN Product_Store 的搜索或筛选条件变更, THE Product_Store SHALL 重置 pageIndex 为 0 并重新加载列表
4. THE Product_Store SHALL 导出纯函数（filterProductsByKeyword、validateCreateProductForm）以支持属性测试

### 需求 14：导航集成

**用户故事：** 作为 ERP 操作员，我希望在侧边栏看到产品管理入口，以便快速访问产品管理功能。

#### 验收标准

1. THE Product_List_View SHALL 通过 /product 路由可访问
2. THE Product_Detail_View SHALL 通过 /product/:productId 路由可访问
3. WHEN 应用加载, THE 导航栏 SHALL 显示"产品管理"菜单项，使用 icons.package 图标
4. WHEN 用户点击"产品管理"导航项, THE 导航栏 SHALL 导航到 /product 路由并显示 ProductPanel 二级面板
