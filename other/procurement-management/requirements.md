---
name: procurement-management
category: other
description: Template for Procurement Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 采购管理模块需求文档 ({{SPEC_NAME_TITLE}})

## 简介

采购管理模块为 Moqui ERP 渐进式现代化项目提供完整的采购管理能力，支持采购申请、采购订单、供应商管理以及收货管理，涵盖从需求到入库的完整采购流程。

## 术语表

- **Procurement_System**: 采购管理模块的后端服务与前端界面的统称
- **PurchaseOrder**: 采购订单，包含订单头信息和订单行项目
- **PurchaseOrderItem**: 采购订单行项目，表示一个采购物料条目
- **PurchaseRequisition**: 采购申请，采购需求的发起单据
- **Supplier**: 供应商，提供物料的外部合作方
- **GoodsReceipt**: 收货记录，采购物料的入库记录
- **Operator**: ERP 系统操作员

## 需求

### 需求 1：采购订单列表查询

**用户故事：** 作为 Operator，我想查看采购订单列表并支持按供应商和状态搜索，以便快速定位目标订单。

#### 验收标准

1. WHEN Operator 请求采购订单列表, THE Procurement_System SHALL 返回分页结果，包含 pageIndex、pageSize、totalCount、totalPages
2. WHEN Operator 提供 supplierId 过滤参数, THE Procurement_System SHALL 仅返回该供应商的采购订单
3. WHEN Operator 提供 statusId 过滤参数, THE Procurement_System SHALL 仅返回匹配状态的采购订单
4. THE Procurement_System SHALL 在每条列表项中返回 orderId、supplierId、supplierName、statusId、orderDate、totalAmount

### 需求 2：采购订单详情查看

**用户故事：** 作为 Operator，我想查看采购订单的完整详情（含行项目），以便了解采购内容。

#### 验收标准

1. WHEN Operator 请求指定 orderId 的详情, THE Procurement_System SHALL 返回订单头信息及其所有 PurchaseOrderItem 列表
2. THE Procurement_System SHALL 在每个 PurchaseOrderItem 中包含 itemSeqId、productId、productName、quantity、unitPrice、amount
3. IF 请求的 orderId 不存在, THEN THE Procurement_System SHALL 返回 404 错误码和描述信息

### 需求 3：创建采购订单

**用户故事：** 作为 Operator，我想创建新的采购订单，以便向供应商下达采购需求。

#### 验收标准

1. WHEN Operator 提交创建请求, THE Procurement_System SHALL 验证 supplierId 为必填字段
2. WHEN 必填字段缺失, THE Procurement_System SHALL 返回 400 错误码并指明缺失字段
3. WHEN 创建请求合法, THE Procurement_System SHALL 创建采购订单并返回新生成的 orderId

### 需求 4：更新采购订单

**用户故事：** 作为 Operator，我想修改采购订单头信息，以便更正或补充数据。

#### 验收标准

1. WHEN Operator 提交更新请求, THE Procurement_System SHALL 仅更新请求中包含的字段
2. IF 目标采购订单不存在, THEN THE Procurement_System SHALL 返回 404 错误码和描述信息
3. WHEN 更新成功, THE Procurement_System SHALL 返回 orderId 和 success 标志

### 需求 5：采购订单项管理

**用户故事：** 作为 Operator，我想管理采购订单中的行项目（添加/修改/删除），以便维护采购内容。

#### 验收标准

1. WHEN Operator 添加 PurchaseOrderItem, THE Procurement_System SHALL 验证 productId 和 quantity 为必填字段
2. WHEN 添加请求合法, THE Procurement_System SHALL 创建行项目并自动生成 itemSeqId
3. WHEN Operator 更新 PurchaseOrderItem, THE Procurement_System SHALL 仅更新请求中包含的字段
4. WHEN Operator 删除 PurchaseOrderItem, THE Procurement_System SHALL 移除该行项目
5. IF 目标采购订单或行项目不存在, THEN THE Procurement_System SHALL 返回 404 错误码

### 需求 6：采购订单状态管理

**用户故事：** 作为 Operator，我想管理采购订单的状态流转，以便跟踪采购进度。

#### 验收标准

1. WHEN Operator 提交状态变更请求, THE Procurement_System SHALL 验证状态流转合法性
2. THE Procurement_System SHALL 支持以下状态: Created → Approved → Sent → PartialReceived → Completed → Cancelled
3. WHEN 状态变更成功, THE Procurement_System SHALL 返回 orderId 和新状态

### 需求 7：前端采购订单列表页

**用户故事：** 作为 Operator，我想在前端界面浏览采购订单列表。

#### 验收标准

1. WHEN Operator 访问采购订单列表页, THE Procurement_System SHALL 展示表格，包含 orderId、供应商名称、状态、订单日期、总金额
2. WHEN Operator 输入搜索关键词, THE Procurement_System SHALL 按供应商过滤列表
3. WHEN Operator 切换分页, THE Procurement_System SHALL 加载对应页的数据
4. WHEN Operator 点击创建按钮, THE Procurement_System SHALL 打开创建对话框

### 需求 8：前端采购订单详情页

**用户故事：** 作为 Operator，我想在前端查看采购订单详情和行项目列表。

#### 验收标准

1. WHEN Operator 进入详情页, THE Procurement_System SHALL 展示订单头信息和行项目列表
2. WHEN Operator 点击添加项目按钮, THE Procurement_System SHALL 提供添加行项目的表单
3. WHEN Operator 点击编辑或删除行项目, THE Procurement_System SHALL 提供对应的操作界面
4. WHEN Operator 点击状态变更按钮, THE Procurement_System SHALL 执行状态流转

### 需求 9：导航集成

**用户故事：** 作为 Operator，我想通过侧边栏导航访问采购管理模块。

#### 验收标准

1. WHEN Operator 查看侧边栏, THE Procurement_System SHALL 在导航中显示采购管理入口
2. WHEN Operator 点击导航项, THE Procurement_System SHALL 路由到采购订单列表页

## 非功能需求

- 遵循现有 OrderService/OrderController 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式（error, errorCode, errorMessage）
