---
name: party-management
category: other
description: Template for Party Management
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档

## 简介

Party（组织与联系人）管理模块，为 331-poc Moqui ERP 现代化项目提供对 Moqui mantle-udm 中 Party 相关实体的现代化管理能力。模块涵盖 Party 列表浏览、详情查看、创建编辑、联系方式管理、角色分配和关系管理等功能，通过 Java REST API 后端和 Vue 3 前端实现。

## 术语表

- **Party**: Moqui 数据模型中的核心实体，代表一个参与方，可以是组织（Organization）或个人（Person），由 `mantle.party.Party` 实体定义
- **Organization**: Party 的子类型，代表组织/公司，由 `mantle.party.Organization` 实体定义，包含 organizationName 等字段
- **Person**: Party 的子类型，代表自然人，由 `mantle.party.Person` 实体定义，包含 firstName、lastName 等字段
- **PartyRole**: Party 的角色分配，由 `mantle.party.PartyRole` 实体定义，关联 Party 与 RoleType
- **RoleType**: 角色类型定义，由 `mantle.party.RoleType` 实体定义，如 Customer、Supplier、Employee 等
- **PartyRelationship**: Party 之间的关系，由 `mantle.party.PartyRelationship` 实体定义，包含 fromPartyId、toPartyId、relationshipTypeEnumId 等
- **ContactMech**: 联系方式机制，由 `mantle.party.contact.ContactMech` 实体定义，通过 contactMechTypeEnumId 区分类型（电话、邮箱、地址等）
- **PartyContactMech**: Party 与 ContactMech 的关联实体，包含用途（contactMechPurposeId）和有效期
- **PostalAddress**: 邮寄地址，ContactMech 的子类型，包含 address1、city、stateProvinceGeoId 等
- **TelecomNumber**: 电话号码，ContactMech 的子类型，包含 countryCode、areaCode、contactNumber
- **PartyService**: 后端服务层，负责通过 Moqui ECFI 访问 Party 相关实体的业务逻辑
- **PartyController**: 后端控制器层，负责处理 Party 相关的 REST API 请求并委托给 PartyService
- **PartyStore**: 前端 Pinia Store，负责管理 Party 相关的状态和 API 调用

## 需求

### 需求 1：Party 列表浏览

**用户故事：** 作为 ERP 操作员，我希望浏览所有 Party（组织与联系人）的列表，以便快速了解系统中的参与方信息。

#### 验收标准

1. WHEN 用户访问 Party 列表页面，THE PartyController SHALL 返回分页的 Party 列表，每页默认 20 条记录
2. WHEN 用户指定页码和每页条数参数，THE PartyController SHALL 返回对应页的 Party 数据及分页元信息（总数、总页数、当前页）
3. WHEN 用户按类型筛选（Organization 或 Person），THE PartyService SHALL 仅返回匹配 partyTypeEnumId 的 Party 记录
4. WHEN 用户按状态筛选（启用或禁用），THE PartyService SHALL 仅返回匹配 disabled 字段值的 Party 记录
5. WHEN 用户输入搜索关键词，THE PartyService SHALL 返回名称匹配的 Party 记录（Organization 匹配 organizationName，Person 匹配 firstName 或 lastName）
6. WHEN Party 列表返回时，THE PartyController SHALL 为每条记录包含 partyId、pseudoId、partyTypeEnumId、disabled 状态以及显示名称（Organization 为 organizationName，Person 为 firstName + lastName）

### 需求 2：Party 详情查看

**用户故事：** 作为 ERP 操作员，我希望查看某个 Party 的完整详情，以便了解其基本信息、联系方式、角色和关系。

#### 验收标准

1. WHEN 用户请求查看指定 partyId 的 Party 详情，THE PartyService SHALL 返回 Party 基本信息，包括类型特定字段（Organization 的 organizationName 或 Person 的 firstName、lastName 等）
2. WHEN 查询 Party 详情时，THE PartyService SHALL 同时返回该 Party 关联的所有有效联系方式（通过 PartyContactMech 关联，thruDate 为空或大于当前时间），包括联系方式类型、用途和具体内容
3. WHEN 查询 Party 详情时，THE PartyService SHALL 同时返回该 Party 的所有角色（PartyRole），包括 roleTypeId 和角色描述
4. WHEN 查询 Party 详情时，THE PartyService SHALL 同时返回该 Party 的所有关系（PartyRelationship），包括关系类型、对方 Party 信息和关系状态
5. IF 指定的 partyId 不存在，THEN THE PartyController SHALL 返回 404 状态码和描述性错误信息

### 需求 3：Party 创建

**用户故事：** 作为 ERP 操作员，我希望创建新的 Organization 或 Person 类型的 Party，以便将新的参与方纳入系统管理。

#### 验收标准

1. WHEN 用户提交创建 Organization 的请求（包含 organizationName），THE PartyService SHALL 创建 Party 记录（partyTypeEnumId 为 PtyOrganization）和对应的 Organization 记录，并返回新创建的 partyId
2. WHEN 用户提交创建 Person 的请求（包含 firstName 和 lastName），THE PartyService SHALL 创建 Party 记录（partyTypeEnumId 为 PtyPerson）和对应的 Person 记录，并返回新创建的 partyId
3. WHEN 创建 Organization 时缺少 organizationName，THE PartyController SHALL 返回 400 状态码和验证错误信息
4. WHEN 创建 Person 时缺少 firstName 或 lastName，THE PartyController SHALL 返回 400 状态码和验证错误信息

### 需求 4：Party 编辑

**用户故事：** 作为 ERP 操作员，我希望编辑已有 Party 的基本信息，以便保持参与方数据的准确性。

#### 验收标准

1. WHEN 用户提交更新 Organization 信息的请求，THE PartyService SHALL 更新 Organization 记录的 organizationName 等字段
2. WHEN 用户提交更新 Person 信息的请求，THE PartyService SHALL 更新 Person 记录的 firstName、lastName 等字段
3. WHEN 用户切换 Party 的启用/禁用状态，THE PartyService SHALL 更新 Party 记录的 disabled 字段
4. IF 更新请求中的 partyId 不存在，THEN THE PartyController SHALL 返回 404 状态码和描述性错误信息

### 需求 5：联系方式管理

**用户故事：** 作为 ERP 操作员，我希望管理 Party 的联系方式（电话、邮箱、地址），以便维护完整的联系信息。

#### 验收标准

1. WHEN 用户为 Party 添加电话号码，THE PartyService SHALL 创建 ContactMech（类型 CmtTelecomNumber）、TelecomNumber 和 PartyContactMech 记录
2. WHEN 用户为 Party 添加邮箱地址，THE PartyService SHALL 创建 ContactMech（类型 CmtEmailAddress，infoString 存储邮箱）和 PartyContactMech 记录
3. WHEN 用户为 Party 添加邮寄地址，THE PartyService SHALL 创建 ContactMech（类型 CmtPostalAddress）、PostalAddress 和 PartyContactMech 记录
4. WHEN 用户更新已有联系方式，THE PartyService SHALL 更新对应的 ContactMech 及其子类型记录
5. WHEN 用户删除联系方式，THE PartyService SHALL 将对应 PartyContactMech 记录的 thruDate 设置为当前时间（软删除）
6. IF 添加邮箱时 infoString 格式无效，THEN THE PartyController SHALL 返回 400 状态码和验证错误信息

### 需求 6：角色管理

**用户故事：** 作为 ERP 操作员，我希望管理 Party 的角色分配，以便正确标识参与方在业务中的职能。

#### 验收标准

1. WHEN 用户为 Party 分配角色，THE PartyService SHALL 创建 PartyRole 记录，关联 partyId 和 roleTypeId
2. WHEN 用户移除 Party 的角色，THE PartyService SHALL 删除对应的 PartyRole 记录
3. WHEN 查询可用角色列表时，THE PartyService SHALL 返回所有 RoleType 记录，包含 roleTypeId 和 description
4. IF 为 Party 分配已存在的角色，THEN THE PartyService SHALL 返回提示信息表明角色已存在，不创建重复记录

### 需求 7：Party 关系管理

**用户故事：** 作为 ERP 操作员，我希望管理 Party 之间的关系（如客户-供应商），以便追踪业务伙伴之间的关联。

#### 验收标准

1. WHEN 用户建立两个 Party 之间的关系，THE PartyService SHALL 创建 PartyRelationship 记录，包含 fromPartyId、toPartyId、relationshipTypeEnumId 和 fromDate
2. WHEN 用户解除两个 Party 之间的关系，THE PartyService SHALL 将对应 PartyRelationship 记录的 thruDate 设置为当前时间（软删除）
3. WHEN 查询可用关系类型时，THE PartyService SHALL 返回 PartyRelationshipType 枚举列表
4. IF 建立关系时 fromPartyId 或 toPartyId 不存在，THEN THE PartyController SHALL 返回 404 状态码和描述性错误信息

### 需求 8：前端 Party 列表页面

**用户故事：** 作为 ERP 操作员，我希望通过现代化的 Web 界面浏览和搜索 Party 列表，以便高效地查找和管理参与方。

#### 验收标准

1. WHEN 用户导航到 Party 管理页面，THE PartyStore SHALL 自动加载第一页 Party 数据并展示在表格中
2. WHEN 用户在搜索框输入关键词，THE PartyStore SHALL 在 300ms 防抖后发起搜索请求并更新列表
3. WHEN 用户选择类型或状态筛选条件，THE PartyStore SHALL 发起带筛选参数的请求并更新列表
4. WHEN Party 列表加载中，THE 前端 SHALL 显示加载指示器
5. IF API 请求失败，THEN THE 前端 SHALL 显示错误信息并提供重试按钮

### 需求 9：前端 Party 详情与编辑页面

**用户故事：** 作为 ERP 操作员，我希望通过 Web 界面查看和编辑 Party 的完整信息，以便进行日常数据维护。

#### 验收标准

1. WHEN 用户点击 Party 列表中的某条记录，THE 前端 SHALL 导航到该 Party 的详情页面，展示基本信息、联系方式、角色和关系
2. WHEN 用户在详情页面点击编辑按钮，THE 前端 SHALL 切换到编辑模式，允许修改 Party 基本信息
3. WHEN 用户提交编辑表单，THE PartyStore SHALL 调用更新 API 并在成功后刷新详情数据
4. WHEN 用户在详情页面管理联系方式（添加/编辑/删除），THE PartyStore SHALL 调用对应的联系方式 API 并更新显示
5. WHEN 用户在详情页面管理角色（分配/移除），THE PartyStore SHALL 调用对应的角色 API 并更新显示
6. WHEN 用户在详情页面管理关系（建立/解除），THE PartyStore SHALL 调用对应的关系 API 并更新显示

### 需求 10：前端路由与导航集成

**用户故事：** 作为 ERP 操作员，我希望通过导航菜单快速访问 Party 管理功能，以便融入日常工作流程。

#### 验收标准

1. THE 前端 SHALL 在主导航菜单中添加"Party 管理"入口
2. THE 前端 SHALL 注册 Party 列表页面路由（/party）和 Party 详情页面路由（/party/:partyId）
3. WHEN 用户点击导航菜单中的"Party 管理"，THE 前端 SHALL 导航到 Party 列表页面
