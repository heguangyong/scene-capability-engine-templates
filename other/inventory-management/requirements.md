---
name: inventory-management
category: other
description: Template for Inventory Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 库存管理模块需求文档 ({{SPEC_NAME_TITLE}})

## 简介

库存管理模块为 Moqui ERP 渐进式现代化项目提供完整的库存管理能力，支持库存查询、库存移动、库存调整以及批次管理，涵盖从入库到出库的完整库存操作。

## 术语表

- **Inventory_System**: 库存管理模块的后端服务与前端界面的统称
- **AssetRecord**: 库存资产记录，表示某设施中某产品的库存信息
- **InventoryMovement**: 库存移动，表示库存在设施间的转移
- **InventoryAdjustment**: 库存调整，表示库存数量的手动修正
- **Batch**: 批次，物料的批次追踪标识
- **Operator**: ERP 系统操作员

## 需求

### 需求 1：库存列表查询

**用户故事：** 作为 Operator，我想查看库存列表并支持按产品和设施搜索，以便快速了解库存状况。

#### 验收标准

1. WHEN Operator 请求库存列表, THE Inventory_System SHALL 返回分页结果，包含 pageIndex、pageSize、totalCount、totalPages
2. WHEN Operator 提供 productId 过滤参数, THE Inventory_System SHALL 仅返回该产品的库存记录
3. WHEN Operator 提供 facilityId 过滤参数, THE Inventory_System SHALL 仅返回该设施的库存记录
4. THE Inventory_System SHALL 在每条列表项中返回 assetId、productId、productName、facilityId、facilityName、quantityOnHand、availableToPromise、statusId

### 需求 2：库存详情查看

**用户故事：** 作为 Operator，我想查看库存记录的完整详情，以便了解库存明细。

#### 验收标准

1. WHEN Operator 请求指定 assetId 的详情, THE Inventory_System SHALL 返回完整的库存信息
2. THE Inventory_System SHALL 返回 assetId、productId、productName、facilityId、facilityName、quantityOnHand、availableToPromise、statusId、lotId、receivedDate、manufacturedDate
3. IF 请求的 assetId 不存在, THEN THE Inventory_System SHALL 返回 404 错误码和描述信息

### 需求 3：库存移动

**用户故事：** 作为 Operator，我想执行库存移动操作，以便在设施间转移物料。

#### 验收标准

1. WHEN Operator 提交移动请求, THE Inventory_System SHALL 验证 assetId、toFacilityId、quantity 为必填字段
2. WHEN 必填字段缺失, THE Inventory_System SHALL 返回 400 错误码并指明缺失字段
3. WHEN 移动请求合法, THE Inventory_System SHALL 创建移动记录并更新库存数量
4. WHEN 移动数量超过可用库存, THE Inventory_System SHALL 返回 400 错误码

### 需求 4：库存调整

**用户故事：** 作为 Operator，我想执行库存调整操作，以便修正库存差异。

#### 验收标准

1. WHEN Operator 提交调整请求, THE Inventory_System SHALL 验证 assetId、adjustQuantity、reason 为必填字段
2. WHEN 调整请求合法, THE Inventory_System SHALL 更新库存数量并创建调整记录
3. WHEN 调整后库存为负数, THE Inventory_System SHALL 返回 400 错误码

### 需求 5：批次管理

**用户故事：** 作为 Operator，我想管理库存的批次信息，以便追踪物料来源。

#### 验收标准

1. WHEN Operator 查询批次列表, THE Inventory_System SHALL 返回按批次分组的库存信息
2. THE Inventory_System SHALL 在每条批次记录中包含 lotId、productId、productName、quantity、manufacturedDate、expirationDate
3. WHEN Operator 提供 productId 过滤参数, THE Inventory_System SHALL 仅返回该产品的批次记录

### 需求 6：前端库存列表页

**用户故事：** 作为 Operator，我想在前端界面浏览库存列表。

#### 验收标准

1. WHEN Operator 访问库存列表页, THE Inventory_System SHALL 展示表格，包含产品名称、设施名称、在手数量、可用数量、状态
2. WHEN Operator 输入搜索关键词, THE Inventory_System SHALL 按产品或设施过滤列表
3. WHEN Operator 切换分页, THE Inventory_System SHALL 加载对应页的数据
4. WHEN Operator 点击移动按钮, THE Inventory_System SHALL 打开库存移动对话框

### 需求 7：前端库存详情页

**用户故事：** 作为 Operator，我想在前端查看库存详情和操作历史。

#### 验收标准

1. WHEN Operator 进入详情页, THE Inventory_System SHALL 展示库存完整信息
2. WHEN Operator 点击调整按钮, THE Inventory_System SHALL 提供库存调整表单
3. WHEN Operator 点击移动按钮, THE Inventory_System SHALL 提供库存移动表单

### 需求 8：导航集成

**用户故事：** 作为 Operator，我想通过侧边栏导航访问库存管理模块。

#### 验收标准

1. WHEN Operator 查看侧边栏, THE Inventory_System SHALL 在导航中显示库存管理入口
2. WHEN Operator 点击导航项, THE Inventory_System SHALL 路由到库存列表页

## 非功能需求

- 遵循现有 OrderService/OrderController 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构
- 所有 API 遵循统一的错误响应格式（error, errorCode, errorMessage）
