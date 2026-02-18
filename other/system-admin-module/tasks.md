---
name: system-admin-module
category: other
description: Template for System Admin Module
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 实现计划: 系统管理模块

## 概述

基于需求和设计文档，将系统管理模块拆分为增量式编码任务。后端使用 Java 21，前端使用 TypeScript/Vue 3。复用已有的 Generic CRUD API 处理标准实体操作，仅为超出 CRUD 的操作新建服务。

## 任务

- [x] 1. 后端：UserManagementService 实现
  - [x] 1.1 创建 UserManagementService 类
    - 在 `backend/app/src/main/java/org/moqui/rest/service/` 下创建
    - 实现 `toggleUserStatus(String userId)` 方法：查找 UserAccount，切换 disabled 字段
    - 实现 `resetPassword(String userId, String newPassword)` 方法：验证密码长度 ≥ 6，使用 Moqui 密码加密，重置 successiveFailedLogins
    - 遵循 disableAuthz/enableAuthz 的 try-finally 模式（参考 AuthService）
    - 用户不存在时返回 404 错误
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 9.1_

  - [x]* 1.2 编写 UserManagementService 属性测试
    - **Property 3: 用户状态切换往返一致性**
    - **Property 4: 密码重置后失败计数归零**
    - **Property 5: 短密码拒绝**
    - **Validates: Requirements 3.1, 3.2, 3.3, 3.4**

  - [x]* 1.3 编写 UserManagementService 单元测试
    - 测试 toggleUserStatus 正常路径和用户不存在路径
    - 测试 resetPassword 正常路径、短密码拒绝、用户不存在路径
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_

- [x] 2. 后端：SystemConfigService 实现
  - [x] 2.1 创建 SystemConfigService 类
    - 在 `backend/app/src/main/java/org/moqui/rest/service/` 下创建
    - 定义 CONFIG_WHITELIST 白名单常量
    - 实现 `getConfig()` 方法：读取 application.properties，过滤白名单项
    - 实现 `updateConfig(Map<String, String> updates)` 方法：验证键在白名单内，原子更新 properties 文件
    - IO 错误时保持原有配置不变
    - _Requirements: 6.2, 6.4, 6.5, 9.2_

  - [x]* 2.2 编写 SystemConfigService 属性测试
    - **Property 6: 配置白名单强制执行**
    - **Property 7: 配置更新往返一致性**
    - **Property 8: 配置更新原子性**
    - **Validates: Requirements 6.2, 6.4, 6.5**

  - [x]* 2.3 编写 SystemConfigService 单元测试
    - 测试 getConfig 正常路径
    - 测试 updateConfig 正常路径、白名单外键拒绝、IO 错误处理
    - _Requirements: 6.2, 6.4, 6.5_

- [x] 3. 后端：AdminController 实现与路由注册
  - [x] 3.1 创建 AdminController 类
    - 在 `backend/app/src/main/java/org/moqui/rest/controller/` 下创建
    - 注册路由：POST `/api/v1/admin/users/{userId}/toggle-status`
    - 注册路由：POST `/api/v1/admin/users/{userId}/reset-password`
    - 注册路由：GET `/api/v1/admin/config`
    - 注册路由：PUT `/api/v1/admin/config`
    - 实现请求参数解析和响应构造（参考 AuthController 模式）
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 6.2, 6.5_

  - [x] 3.2 在 RestApplication 中注册 AdminController
    - 创建 UserManagementService 和 SystemConfigService 实例
    - 创建 AdminController 并调用 register(router)
    - _Requirements: 9.1, 9.2, 9.3_

  - [x]* 3.3 编写 AdminController 路由注册测试
    - **Property 11: 管理接口认证保护**
    - 验证所有 admin 路由已注册且非 public
    - **Validates: Requirements 9.3**

- [x] 4. 检查点 - 后端编译和测试
  - 确保 `mvn clean package` 编译通过
  - 确保 `mvn test` 所有测试通过
  - 如有问题请询问用户

- [x] 5. 前端：类型定义和 API 服务
  - [x] 5.1 创建前端类型定义
    - 在 `frontend/src/renderer/types/` 下创建 `admin.ts`
    - 定义 UserAccount、SecurityGroup、UserGroupMember、ConfigItem、AuditLogEntry 等接口
    - _Requirements: 1.1, 4.1, 6.1, 7.1_

  - [x] 5.2 创建 adminApiService
    - 在 `frontend/src/renderer/services/` 下创建 `adminApiService.ts`
    - 实现 toggleUserStatus、resetPassword、getConfig、updateConfig 方法
    - 使用 apiV1Client 调用后端 Admin API
    - _Requirements: 3.1, 3.2, 3.3, 6.2_

- [x] 6. 前端：Pinia Stores
  - [x] 6.1 创建 userAdminStore
    - 在 `frontend/src/renderer/stores/` 下创建 `userAdmin.ts`
    - 管理用户列表、搜索、分页、排序状态
    - 使用 entityService 调用 Generic CRUD API 进行用户 CRUD
    - 使用 adminApiService 调用 toggle-status 和 reset-password
    - 实现搜索过滤逻辑（按用户名或邮箱）
    - 实现表单验证函数（必填字段检查）
    - _Requirements: 1.1, 1.2, 1.3, 2.1, 2.3, 3.1, 3.3_

  - [x]* 6.2 编写 userAdminStore 属性测试
    - **Property 1: 用户搜索过滤正确性**
    - **Property 2: 必填字段验证完整性**
    - **Validates: Requirements 1.2, 2.3**

  - [x] 6.3 创建 roleAdminStore
    - 在 `frontend/src/renderer/stores/` 下创建 `roleAdmin.ts`
    - 管理安全组列表、权限列表、成员列表状态
    - 使用 entityService 调用 Generic CRUD API
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 5.1, 5.2, 5.3_

  - [x] 6.4 创建 configAdminStore
    - 在 `frontend/src/renderer/stores/` 下创建 `configAdmin.ts`
    - 管理系统配置列表、加载、更新状态
    - 使用 adminApiService 调用配置 API
    - _Requirements: 6.1, 6.2, 6.3_

  - [x] 6.5 创建 auditLogStore
    - 在 `frontend/src/renderer/stores/` 下创建 `auditLog.ts`
    - 管理审计日志列表、过滤、分页状态（默认每页 50 条）
    - 使用 entityService 调用 Generic CRUD API
    - 实现过滤逻辑（时间范围、实体名称、操作用户）
    - 只读模式，不暴露修改/删除方法
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5_

  - [x]* 6.6 编写 auditLogStore 属性测试
    - **Property 9: 审计日志过滤正确性**
    - **Validates: Requirements 7.2, 7.3**

- [x] 7. 前端：Vue 页面组件
  - [x] 7.1 创建 UserListView.vue
    - 用户列表表格（用户名、用户ID、邮箱、状态、创建时间）
    - 搜索输入框、分页导航、排序功能
    - 创建/编辑用户对话框（UserFormDialog）
    - 启用/禁用切换、密码重置操作
    - 错误提示和重试按钮
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3_

  - [x] 7.2 创建 RoleListView.vue 和 RoleDetailView.vue
    - RoleListView: 安全组列表（组ID、描述、成员数量）
    - RoleDetailView: 安全组详情（权限列表 + 成员列表）
    - 添加/移除权限、添加/移除成员操作
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 5.1, 5.2, 5.3_

  - [x] 7.3 创建 SystemConfigView.vue
    - 配置参数列表（参数名、当前值、描述）
    - 编辑配置值、提交更新
    - 成功/错误提示
    - _Requirements: 6.1, 6.2, 6.3_

  - [x] 7.4 创建 AuditLogView.vue
    - 审计日志表格（时间戳、操作用户、实体名称、操作类型、变更内容）
    - 时间范围筛选、实体名称筛选、操作用户筛选
    - 分页导航（默认 50 条/页）
    - 只读展示，无编辑/删除按钮
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5_

- [x] 8. 前端：路由和导航集成
  - [x] 8.1 更新路由配置
    - 在 `frontend/src/renderer/router/index.js` 中添加系统管理路由
    - `/admin/users` → UserListView
    - `/admin/roles` → RoleListView
    - `/admin/roles/:groupId` → RoleDetailView
    - `/admin/config` → SystemConfigView
    - `/admin/audit` → AuditLogView
    - 所有路由设置 `meta: { requiresAuth: true }`
    - _Requirements: 8.1, 8.2_

  - [x] 8.2 更新导航配置
    - 在 `frontend/src/renderer/stores/navigation.ts` 中添加"系统管理"导航分组
    - 包含用户管理、角色管理、系统设置、审计日志子项
    - 更新 NavId 类型和 PanelContext 类型
    - _Requirements: 8.1, 8.2, 8.3_

- [x] 9. 最终检查点 - 全量编译和测试
  - 确保后端 `mvn clean package && mvn test` 全部通过
  - 确保前端 `npm run build && npm test` 全部通过
  - 如有问题请询问用户

## 备注

- 标记 `*` 的任务为可选任务，可跳过以加速 MVP 交付
- 每个任务引用具体需求编号以确保可追溯性
- 检查点确保增量验证
- 属性测试验证通用正确性属性，单元测试验证具体示例和边界情况
- 大量 CRUD 操作复用已有的 Generic CRUD API，无需重复实现
