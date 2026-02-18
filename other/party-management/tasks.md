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

﻿# 实现计划：Party（组织与联系人）管理模块

## 概述

基于已批准的需求和设计文档，将 Party 管理模块分解为增量式编码任务。后端采用 Java（PartyController + PartyService），前端采用 TypeScript/Vue 3（partyApiService + partyStore + Vue 页面）。每个任务构建在前一个任务之上，确保无孤立代码。

## 任务

- [x] 1. 后端 PartyService 核心实现
  - [x] 1.1 创建 PartyService 类，实现 listParties 方法
    - 创建 `backend/app/src/main/java/org/moqui/rest/service/PartyService.java`
    - 注入 ExecutionContextFactory，实现分页查询 Party 列表
    - 联合查询 Party + Organization/Person，构建 displayName
    - 支持 partyType、disabled 筛选和 searchKeyword 搜索
    - 返回 `Map<String, Object>` 包含 data 列表和分页元信息
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6_

  - [x] 1.2 实现 getPartyDetail 方法
    - 查询 Party 基本信息 + Organization/Person 类型特定字段
    - 查询有效联系方式（PartyContactMech.thruDate 为空或大于当前时间）
    - 联合 ContactMech + TelecomNumber/PostalAddress/infoString
    - 查询 PartyRole + RoleType description
    - 查询 PartyRelationship + 对方 Party 信息
    - 不存在时返回 RESOURCE_NOT_FOUND 错误
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_

  - [x] 1.3 实现 createParty 方法
    - 根据 partyType 创建 Party 记录 + Organization 或 Person 记录
    - Organization 必须有 organizationName，Person 必须有 firstName + lastName
    - 返回新创建的 partyId
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 1.4 实现 updateParty 和 toggleStatus 方法
    - updateParty：根据 partyType 更新 Organization 或 Person 字段
    - toggleStatus：翻转 Party.disabled 字段（Y↔N）
    - 不存在时返回 RESOURCE_NOT_FOUND
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [x] 2. 后端联系方式、角色、关系管理
  - [x] 2.1 实现联系方式管理方法（addContactMech, updateContactMech, deleteContactMech）
    - addContactMech：根据 contactMechTypeEnumId 创建 ContactMech + 子类型 + PartyContactMech
    - updateContactMech：更新 ContactMech 及子类型记录
    - deleteContactMech：设置 PartyContactMech.thruDate 为当前时间（软删除）
    - 邮箱格式验证
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6_

  - [x] 2.2 实现角色管理方法（addRole, removeRole, listRoleTypes）
    - addRole：创建 PartyRole，重复分配返回 DUPLICATE_ERROR
    - removeRole：删除 PartyRole 记录
    - listRoleTypes：查询所有 RoleType
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [x] 2.3 实现关系管理方法（addRelationship, removeRelationship, listRelationshipTypes）
    - addRelationship：创建 PartyRelationship，验证 fromPartyId/toPartyId 存在
    - removeRelationship：设置 thruDate 为当前时间（软删除）
    - listRelationshipTypes：查询 PartyRelationshipType 枚举
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

- [x] 3. 后端 PartyController 实现与注册
  - [x] 3.1 创建 PartyController，注册所有 REST 路由
    - 创建 `backend/app/src/main/java/org/moqui/rest/controller/PartyController.java`
    - 注册设计文档中定义的所有 15 个 REST 端点
    - 实现请求参数解析、验证和错误响应映射
    - 遵循 AdminController 的错误处理模式（mapErrorCodeToStatus）
    - _Requirements: 1.1-7.4_

  - [x] 3.2 在应用启动类中注册 PartyController
    - 在 RestApplication 或等效启动类中实例化 PartyService 和 PartyController
    - 调用 partyController.register(router) 注册路由
    - _Requirements: 1.1-7.4_

- [x] 4. 后端单元测试
  - [x]* 4.1 编写 PartyService 单元测试
    - 创建 `backend/app/src/test/java/org/moqui/rest/service/PartyServiceTest.java`
    - 使用 Mockito mock ExecutionContextFactory 和 Entity Facade
    - 测试 listParties 的分页、筛选、搜索逻辑
    - 测试 createParty/updateParty 的正常和验证失败场景
    - 测试联系方式、角色、关系的 CRUD 操作
    - 测试不存在 partyId 的 404 场景
    - _Requirements: 1.1-7.4_

  - [x]* 4.2 编写 PartyController 单元测试
    - 创建 `backend/app/src/test/java/org/moqui/rest/controller/PartyControllerTest.java`
    - 测试路由注册和请求参数解析
    - 测试错误响应格式
    - _Requirements: 1.1-7.4_

- [x]* 5. Checkpoint — 后端完成验证
  - Ensure all tests pass, ask the user if questions arise.
  - ⚠️ `mvn -pl app test` 发现现有非 Party 相关失败：`ApiCatalogControllerWorkflowPropertyTest`、`EndpointRegressionPropertyTest`、`ServiceCatalogServiceInternalsPropertyTest`、`ServiceCatalogServiceSnapshotWorkflowPropertyTest`

- [x] 6. 前端类型定义和 API 服务
  - [x] 6.1 创建 Party 类型定义
    - 创建 `frontend/src/renderer/types/party.ts`
    - 定义 PartyListItem, PartyDetail, ContactMechItem, PartyRoleItem, PartyRelationshipItem
    - 定义 RoleType, RelationshipType, PartyListParams
    - 定义请求类型 CreatePartyRequest, UpdatePartyRequest, ContactMechRequest, RelationshipRequest
    - _Requirements: 1.6, 2.1-2.4_

  - [x] 6.2 创建 partyApiService
    - 创建 `frontend/src/renderer/services/partyApiService.ts`
    - 封装所有 Party REST API 调用（列表、详情、CRUD、联系方式、角色、关系）
    - 使用 apiV1Client 发起请求
    - _Requirements: 1.1-7.4_

- [x] 7. 前端 Pinia Store
  - [x] 7.1 创建 partyStore
    - 创建 `frontend/src/renderer/stores/party.ts`
    - 实现状态管理：parties 列表、pagination、loading、error、filters
    - 实现 actions：fetchParties、searchParties、setPage、setFilter
    - 实现详情 actions：fetchPartyDetail、createParty、updateParty、toggleStatus
    - 实现联系方式/角色/关系管理 actions
    - 导出纯函数：filterPartiesByKeyword、validateCreatePartyForm、validateEmail、buildDisplayName
    - _Requirements: 8.1, 8.2, 8.3, 9.3-9.6_

  - [x]* 7.2 编写前端纯函数属性测试
    - 使用 fast-check 编写属性测试
    - **Property 12: validateCreatePartyForm 验证正确性**
    - **Validates: Requirements 3.3, 3.4**
    - **Property 13: filterPartiesByKeyword 过滤正确性**
    - **Validates: Requirements 1.5, 8.3**
    - **Property 14: buildDisplayName 构建正确性**
    - **Validates: Requirements 1.6**
    - **Property 15: validateEmail 验证正确性**
    - **Validates: Requirements 5.6**

- [x] 8. 前端 Party 列表页面
  - [x] 8.1 创建 PartyListView 组件
    - 创建 `frontend/src/renderer/views/PartyListView.vue`
    - 表格展示 Party 列表（partyId、显示名称、类型、状态）
    - 搜索框（300ms 防抖）+ 类型筛选下拉 + 状态筛选下拉
    - 分页控件
    - 创建 Party 按钮 → 弹出创建对话框
    - 点击行 → 导航到详情页
    - 加载指示器和错误提示
    - _Requirements: 8.1, 8.2, 8.3, 8.4, 8.5_

  - [x] 8.2 创建 PartyFormDialog 组件
    - 创建 `frontend/src/renderer/views/PartyFormDialog.vue`
    - 支持创建 Organization（organizationName）和 Person（firstName + lastName）
    - 类型选择切换表单字段
    - 表单验证（使用 validateCreatePartyForm）
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [x] 9. 前端 Party 详情页面
  - [x] 9.1 创建 PartyDetailView 组件
    - 创建 `frontend/src/renderer/views/PartyDetailView.vue`
    - Tab 布局：基本信息 | 联系方式 | 角色 | 关系
    - 基本信息 Tab：展示/编辑 Party 信息，启用/禁用切换
    - 联系方式 Tab：列表展示 + 添加/编辑/删除操作
    - 角色 Tab：已分配角色列表 + 分配/移除操作
    - 关系 Tab：关系列表 + 建立/解除操作
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5, 9.6_

- [x] 10. 前端路由与导航集成
  - [x] 10.1 注册 Party 路由并添加导航入口
    - 在 `frontend/src/renderer/router/index.js` 添加 Party 路由
    - `/party` → PartyListView（懒加载）
    - `/party/:partyId` → PartyDetailView（懒加载）
    - 在导航菜单组件中添加"Party 管理"入口
    - _Requirements: 10.1, 10.2, 10.3_

- [x] 11. Final checkpoint — 全量测试验证
  - Ensure all tests pass, ask the user if questions arise.

## 备注

- 标记 `*` 的任务为可选测试任务，可跳过以加速 MVP
- 每个任务引用具体需求以确保可追溯性
- Checkpoint 确保增量验证
- 属性测试验证通用正确性属性，单元测试验证具体示例和边界条件
