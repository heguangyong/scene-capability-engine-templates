---
name: bom-management
category: other
description: Template for Bom Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# BOM 管理模块需求文档 (Bill of Materials Management)

## 简介

BOM（物料清单）管理模块为 Moqui ERP 渐进式现代化项目提供完整的物料清单管理能力，支持生产 BOM、维修 BOM、成本 BOM 等多种类型，涵盖 BOM 头管理、BOM 行项目管理、替代料管理以及联产品/副产品标记。

## 术语表

- **BOM_System**: BOM 管理模块的后端服务与前端界面的统称
- **BOM_Header**: BOM 头信息，包含 BOM 标识、关联产品、BOM 类型等元数据
- **BOM_Item**: BOM 行项目，表示 BOM 中的一个物料条目，包含产品、数量、单位等
- **Substitute**: 替代料，为某个 BOM 行项目设置的可替换物料，含优先级和比例
- **Co_Product**: 联产品，BOM 生产过程中同时产出的主要产品
- **By_Product**: 副产品，BOM 生产过程中附带产出的次要产品
- **BOM_Type**: BOM 类型枚举，包括 PRODUCTION（生产）、REPAIR（维修）、COST（成本）
- **Operator**: ERP 系统操作员

## 需求

### 需求 1：BOM 列表查询

**用户故事：** 作为 Operator，我想查看 BOM 列表并支持按产品搜索和分页，以便快速定位目标 BOM。

#### 验收标准

1. WHEN Operator 请求 BOM 列表, THE BOM_System SHALL 返回分页结果，包含 pageIndex、pageSize、totalCount、totalPages
2. WHEN Operator 提供 productId 过滤参数, THE BOM_System SHALL 仅返回关联该产品的 BOM 记录
3. WHEN Operator 提供 bomType 过滤参数, THE BOM_System SHALL 仅返回匹配该类型的 BOM 记录
4. THE BOM_System SHALL 在每条 BOM 列表项中返回 bomId、productId、productName、bomType、description、fromDate

### 需求 2：BOM 详情查看

**用户故事：** 作为 Operator，我想查看 BOM 的完整详情（含多级树形结构），以便了解物料组成和层级关系。

#### 验收标准

1. WHEN Operator 请求指定 bomId 的详情, THE BOM_System SHALL 返回 BOM 头信息及其所有 BOM_Item 列表
2. THE BOM_System SHALL 在每个 BOM_Item 中包含 itemSeqId、productId、productName、quantity、quantityUomId、fromDate、bomItemType
3. WHEN BOM_Item 存在替代料, THE BOM_System SHALL 在该 BOM_Item 中包含 Substitute 列表，每条含 substituteId、productId、productName、priority、ratio
4. WHEN BOM_Item 的 bomItemType 为 CO_PRODUCT 或 BY_PRODUCT, THE BOM_System SHALL 在返回数据中标记对应类型
5. IF 请求的 bomId 不存在, THEN THE BOM_System SHALL 返回 404 错误码和描述信息

### 需求 3：创建 BOM

**用户故事：** 作为 Operator，我想创建新的 BOM，以便为产品定义物料组成。

#### 验收标准

1. WHEN Operator 提交创建请求, THE BOM_System SHALL 验证 productId 和 bomType 为必填字段
2. WHEN 必填字段缺失, THE BOM_System SHALL 返回 400 错误码并指明缺失字段
3. WHEN 创建请求合法, THE BOM_System SHALL 创建 BOM_Header 并返回新生成的 bomId
4. THE BOM_System SHALL 支持 PRODUCTION、REPAIR、COST 三种 BOM_Type
5. WHEN Operator 提供 description 和 fromDate, THE BOM_System SHALL 将其存储到 BOM_Header 中

### 需求 4：更新 BOM

**用户故事：** 作为 Operator，我想修改 BOM 头信息，以便更正或补充 BOM 数据。

#### 验收标准

1. WHEN Operator 提交更新请求, THE BOM_System SHALL 仅更新请求中包含的字段
2. IF 目标 BOM 不存在, THEN THE BOM_System SHALL 返回 404 错误码和描述信息
3. WHEN 更新成功, THE BOM_System SHALL 返回 bomId 和 success 标志

### 需求 5：BOM 项管理

**用户故事：** 作为 Operator，我想管理 BOM 中的行项目（添加/修改/删除），以便维护物料组成。

#### 验收标准

1. WHEN Operator 添加 BOM_Item, THE BOM_System SHALL 验证 productId 和 quantity 为必填字段
2. WHEN 添加请求合法, THE BOM_System SHALL 创建 BOM_Item 并自动生成 itemSeqId
3. WHEN Operator 更新 BOM_Item, THE BOM_System SHALL 仅更新请求中包含的字段
4. WHEN Operator 删除 BOM_Item, THE BOM_System SHALL 移除该行项目
5. IF 目标 BOM 或 BOM_Item 不存在, THEN THE BOM_System SHALL 返回 404 错误码
6. WHEN Operator 添加 BOM_Item 时指定 bomItemType 为 CO_PRODUCT 或 BY_PRODUCT, THE BOM_System SHALL 将该项标记为联产品或副产品

### 需求 6：替代料管理

**用户故事：** 作为 Operator，我想为 BOM 行项目设置替代物料，以便在主物料缺货时使用替代方案。

#### 验收标准

1. WHEN Operator 添加 Substitute, THE BOM_System SHALL 验证 productId 为必填字段
2. WHEN 添加请求合法, THE BOM_System SHALL 创建 Substitute 记录并关联到指定 BOM_Item
3. WHEN Operator 提供 priority 和 ratio, THE BOM_System SHALL 将其存储到 Substitute 记录中
4. WHEN Operator 删除 Substitute, THE BOM_System SHALL 移除该替代料记录
5. IF 目标 BOM、BOM_Item 或 Substitute 不存在, THEN THE BOM_System SHALL 返回 404 错误码

### 需求 7：前端 BOM 列表页

**用户故事：** 作为 Operator，我想在前端界面浏览 BOM 列表，以便通过可视化方式管理 BOM。

#### 验收标准

1. WHEN Operator 访问 BOM 列表页, THE BOM_System SHALL 展示 BOM 表格，包含 bomId、产品名称、BOM 类型、描述
2. WHEN Operator 输入搜索关键词, THE BOM_System SHALL 按产品过滤 BOM 列表
3. WHEN Operator 切换分页, THE BOM_System SHALL 加载对应页的数据并更新表格
4. WHEN Operator 点击创建按钮, THE BOM_System SHALL 打开 BOM 创建对话框

### 需求 8：前端 BOM 详情页

**用户故事：** 作为 Operator，我想在前端查看 BOM 详情和树形结构，以便直观了解物料层级。

#### 验收标准

1. WHEN Operator 进入 BOM 详情页, THE BOM_System SHALL 展示 BOM 头信息和 BOM_Item 列表
2. WHEN BOM_Item 含有 Substitute, THE BOM_System SHALL 在该行项目下展示替代料信息
3. WHEN BOM_Item 为联产品或副产品, THE BOM_System SHALL 使用视觉标记区分类型
4. WHEN Operator 点击添加项目按钮, THE BOM_System SHALL 提供添加 BOM_Item 的表单
5. WHEN Operator 点击编辑或删除 BOM_Item, THE BOM_System SHALL 提供对应的操作界面

### 需求 9：导航集成

**用户故事：** 作为 Operator，我想通过侧边栏导航访问 BOM 模块，以便快速切换到 BOM 管理功能。

#### 验收标准

1. WHEN Operator 查看侧边栏, THE BOM_System SHALL 在导航中显示 BOM 管理入口
2. WHEN Operator 点击 BOM 导航项, THE BOM_System SHALL 路由到 BOM 列表页
3. THE BOM_System SHALL 在 ContextPanel 中注册 BomPanel 组件

## 非功能需求

- 遵循现有 OrderService/OrderController 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式（error, errorCode, errorMessage）
