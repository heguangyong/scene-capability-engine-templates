---
name: routing-management
category: other
description: Template for Routing Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 工艺路线管理模块需求文档 ({{SPEC_NAME_TITLE}})

## 简介

工艺路线管理模块为 Moqui ERP 渐进式现代化项目提供完整的工艺路线管理能力，支持工艺路线创建、工序管理、工作中心关联以及时间标准设置，涵盖多种生产场景下的工艺路线定义。

## 术语表

- **Routing_System**: 工艺路线管理模块的后端服务与前端界面的统称
- **Routing_Header**: 工艺路线头信息，包含路线标识、关联产品、路线类型等元数据
- **Operation**: 工序，表示工艺路线中的一个加工步骤，包含工作中心、时间标准等
- **Work_Center**: 工作中心，生产设施或资源
- **Time_Standard**: 时间标准，包括准备时间、加工时间、等待时间
- **Operator**: ERP 系统操作员

## 需求

### 需求 1：工艺路线列表查询

**用户故事：** 作为 Operator，我想查看工艺路线列表并支持按产品搜索和分页，以便快速定位目标工艺路线。

#### 验收标准

1. WHEN Operator 请求工艺路线列表, THE Routing_System SHALL 返回分页结果，包含 pageIndex、pageSize、totalCount、totalPages
2. WHEN Operator 提供 productId 过滤参数, THE Routing_System SHALL 仅返回关联该产品的工艺路线记录
3. THE Routing_System SHALL 在每条列表项中返回 routingId、productId、productName、routingName、description、fromDate

### 需求 2：工艺路线详情查看

**用户故事：** 作为 Operator，我想查看工艺路线的完整详情（含工序列表），以便了解加工步骤和时间标准。

#### 验收标准

1. WHEN Operator 请求指定 routingId 的详情, THE Routing_System SHALL 返回路线头信息及其所有 Operation 列表
2. THE Routing_System SHALL 在每个 Operation 中包含 operationSeqId、operationName、workCenterId、workCenterName、setupTime、runTime、waitTime
3. IF 请求的 routingId 不存在, THEN THE Routing_System SHALL 返回 404 错误码和描述信息

### 需求 3：创建工艺路线

**用户故事：** 作为 Operator，我想创建新的工艺路线，以便为产品定义加工流程。

#### 验收标准

1. WHEN Operator 提交创建请求, THE Routing_System SHALL 验证 productId 和 routingName 为必填字段
2. WHEN 必填字段缺失, THE Routing_System SHALL 返回 400 错误码并指明缺失字段
3. WHEN 创建请求合法, THE Routing_System SHALL 创建 Routing_Header 并返回新生成的 routingId

### 需求 4：更新工艺路线

**用户故事：** 作为 Operator，我想修改工艺路线头信息，以便更正或补充数据。

#### 验收标准

1. WHEN Operator 提交更新请求, THE Routing_System SHALL 仅更新请求中包含的字段
2. IF 目标工艺路线不存在, THEN THE Routing_System SHALL 返回 404 错误码和描述信息
3. WHEN 更新成功, THE Routing_System SHALL 返回 routingId 和 success 标志

### 需求 5：工序管理

**用户故事：** 作为 Operator，我想管理工艺路线中的工序（添加/修改/删除），以便维护加工步骤。

#### 验收标准

1. WHEN Operator 添加 Operation, THE Routing_System SHALL 验证 operationName 和 workCenterId 为必填字段
2. WHEN 添加请求合法, THE Routing_System SHALL 创建 Operation 并自动生成 operationSeqId
3. WHEN Operator 更新 Operation, THE Routing_System SHALL 仅更新请求中包含的字段
4. WHEN Operator 删除 Operation, THE Routing_System SHALL 移除该工序
5. IF 目标工艺路线或 Operation 不存在, THEN THE Routing_System SHALL 返回 404 错误码

### 需求 6：前端工艺路线列表页

**用户故事：** 作为 Operator，我想在前端界面浏览工艺路线列表，以便通过可视化方式管理工艺路线。

#### 验收标准

1. WHEN Operator 访问工艺路线列表页, THE Routing_System SHALL 展示表格，包含 routingId、产品名称、路线名称、描述
2. WHEN Operator 输入搜索关键词, THE Routing_System SHALL 按产品过滤列表
3. WHEN Operator 切换分页, THE Routing_System SHALL 加载对应页的数据并更新表格
4. WHEN Operator 点击创建按钮, THE Routing_System SHALL 打开创建对话框

### 需求 7：前端工艺路线详情页

**用户故事：** 作为 Operator，我想在前端查看工艺路线详情和工序列表，以便直观了解加工流程。

#### 验收标准

1. WHEN Operator 进入详情页, THE Routing_System SHALL 展示路线头信息和 Operation 列表
2. WHEN Operator 点击添加工序按钮, THE Routing_System SHALL 提供添加 Operation 的表单
3. WHEN Operator 点击编辑或删除 Operation, THE Routing_System SHALL 提供对应的操作界面

### 需求 8：导航集成

**用户故事：** 作为 Operator，我想通过侧边栏导航访问工艺路线模块。

#### 验收标准

1. WHEN Operator 查看侧边栏, THE Routing_System SHALL 在导航中显示工艺路线管理入口
2. WHEN Operator 点击导航项, THE Routing_System SHALL 路由到工艺路线列表页

## 非功能需求

- 遵循现有 OrderService/OrderController 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式（error, errorCode, errorMessage）
