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

# 需求文档

## 简介

系统管理模块是 331-poc Moqui ERP 现代化项目的第一个真正业务功能模块。该模块提供用户管理、角色/权限管理、用户-组分配、系统配置和审计日志等核心管理功能。后端通过 REST API 暴露管理操作，前端通过 Vue 3 桌面应用提供完整的管理界面。

## 术语表

- **Admin_API**: 系统管理后端 REST API 层，提供用户、角色、权限、配置和审计日志的管理接口
- **Admin_UI**: 系统管理前端 Vue 3 界面，包含用户管理、角色管理、分配管理、系统设置和审计日志页面
- **UserAccount**: Moqui 框架内置的用户账户实体（moqui.security.UserAccount）
- **SecurityGroup**: Moqui 框架内置的安全组实体（moqui.security.SecurityGroup），代表角色
- **SecurityGroupPermission**: Moqui 框架内置的安全组权限实体（moqui.security.SecurityGroupPermission）
- **UserGroupMember**: Moqui 框架内置的用户-组成员关系实体（moqui.security.UserGroupMember）
- **AuditLog**: Moqui 框架内置的审计日志实体（moqui.entity.EntityAuditLog）
- **Generic_CRUD_API**: 已有的通用实体 CRUD API（`/api/v1/entities/{entityName}`）
- **UserManagement_Service**: 用户管理后端服务层，封装超出简单 CRUD 的用户管理操作
- **SystemConfig_Service**: 系统配置后端服务层，管理 application.properties 的读写
- **Admin_Store**: 前端 Pinia Store 层，管理系统管理模块的状态

## 需求

### 需求 1：用户列表与搜索

**用户故事：** 作为系统管理员，我希望查看所有用户账户列表并进行搜索和筛选，以便快速找到需要管理的用户。

#### 验收标准

1. WHEN 管理员访问用户管理页面, THE Admin_UI SHALL 显示 UserAccount 列表，包含用户名、用户ID、邮箱、状态和创建时间字段
2. WHEN 管理员输入搜索关键词, THE Admin_UI SHALL 按用户名或邮箱过滤用户列表
3. WHEN 用户列表数据超过单页容量, THE Admin_UI SHALL 提供分页导航，每页默认显示 20 条记录
4. WHEN 管理员点击列标题, THE Admin_UI SHALL 按该列升序或降序排列用户列表
5. WHEN 用户列表加载过程中发生网络错误, THE Admin_UI SHALL 显示错误提示信息并提供重试按钮

### 需求 2：用户创建与编辑

**用户故事：** 作为系统管理员，我希望创建新用户和编辑现有用户信息，以便管理系统的用户账户。

#### 验收标准

1. WHEN 管理员提交包含用户名、邮箱和初始密码的有效表单, THE Admin_API SHALL 创建新的 UserAccount 记录并返回创建结果
2. WHEN 管理员提交的用户名已存在, THE Admin_API SHALL 返回明确的重复用户名错误信息
3. WHEN 管理员提交的表单缺少必填字段, THE Admin_UI SHALL 在对应字段显示验证错误提示
4. WHEN 管理员修改用户信息并提交, THE Admin_API SHALL 更新对应的 UserAccount 记录
5. WHEN 管理员编辑用户时, THE Admin_UI SHALL 预填充当前用户信息到表单中

### 需求 3：用户启用/禁用与密码重置

**用户故事：** 作为系统管理员，我希望启用或禁用用户账户以及重置用户密码，以便控制用户的系统访问权限。

#### 验收标准

1. WHEN 管理员对一个启用状态的用户执行禁用操作, THE UserManagement_Service SHALL 将该 UserAccount 的 disabled 字段设置为 "Y" 并返回操作结果
2. WHEN 管理员对一个禁用状态的用户执行启用操作, THE UserManagement_Service SHALL 将该 UserAccount 的 disabled 字段设置为 "N" 并返回操作结果
3. WHEN 管理员为指定用户执行密码重置操作, THE UserManagement_Service SHALL 使用 Moqui 的密码加密机制设置新密码并重置 successiveFailedLogins 为 0
4. WHEN 密码重置请求中的新密码长度少于 6 个字符, THE UserManagement_Service SHALL 返回密码长度不足的错误信息
5. IF 启用/禁用或密码重置操作指定的用户不存在, THEN THE UserManagement_Service SHALL 返回 404 用户未找到错误

### 需求 4：安全组（角色）管理

**用户故事：** 作为系统管理员，我希望管理安全组及其权限，以便定义不同角色的访问控制策略。

#### 验收标准

1. WHEN 管理员访问角色管理页面, THE Admin_UI SHALL 显示所有 SecurityGroup 列表，包含组ID、描述和组内用户数量
2. WHEN 管理员提交包含组ID和描述的有效表单, THE Admin_API SHALL 通过 Generic_CRUD_API 创建新的 SecurityGroup 记录
3. WHEN 管理员查看某个安全组的详情, THE Admin_UI SHALL 显示该组的权限列表和成员列表
4. WHEN 管理员为安全组添加权限, THE Admin_API SHALL 通过 Generic_CRUD_API 创建新的 SecurityGroupPermission 记录
5. WHEN 管理员从安全组移除权限, THE Admin_API SHALL 通过 Generic_CRUD_API 删除对应的 SecurityGroupPermission 记录
6. WHEN 管理员提交的安全组ID已存在, THE Admin_API SHALL 返回明确的重复组ID错误信息

### 需求 5：用户-组分配管理

**用户故事：** 作为系统管理员，我希望将用户分配到安全组或从安全组中移除，以便管理用户的角色和权限。

#### 验收标准

1. WHEN 管理员在用户详情页选择安全组并确认分配, THE Admin_API SHALL 通过 Generic_CRUD_API 创建新的 UserGroupMember 记录
2. WHEN 管理员在安全组详情页选择用户并确认分配, THE Admin_API SHALL 通过 Generic_CRUD_API 创建新的 UserGroupMember 记录
3. WHEN 管理员移除用户的安全组分配, THE Admin_API SHALL 通过 Generic_CRUD_API 删除对应的 UserGroupMember 记录
4. WHEN 查询某用户的安全组列表, THE Admin_API SHALL 通过 Generic_CRUD_API 的关联查询返回该用户所属的所有 SecurityGroup
5. WHEN 查询某安全组的成员列表, THE Admin_API SHALL 通过 Generic_CRUD_API 的关联查询返回该组的所有 UserAccount 成员
6. IF 分配操作中指定的用户或安全组不存在, THEN THE Admin_API SHALL 返回 404 资源未找到错误

### 需求 6：系统配置管理

**用户故事：** 作为系统管理员，我希望查看和修改系统配置参数，以便调整系统运行行为。

#### 验收标准

1. WHEN 管理员访问系统设置页面, THE Admin_UI SHALL 显示当前系统配置参数列表，包含参数名、当前值和描述
2. WHEN 管理员修改配置参数值并提交, THE SystemConfig_Service SHALL 更新 application.properties 中对应的配置项并返回更新结果
3. WHEN 配置更新成功, THE Admin_UI SHALL 显示成功提示信息
4. IF 配置更新过程中发生 IO 错误, THEN THE SystemConfig_Service SHALL 返回配置更新失败的错误信息，保持原有配置不变
5. THE SystemConfig_Service SHALL 仅暴露预定义的安全配置项白名单，拒绝对白名单外配置项的读写操作

### 需求 7：审计日志查看

**用户故事：** 作为系统管理员，我希望查看系统审计日志，以便追踪用户操作和数据变更历史。

#### 验收标准

1. WHEN 管理员访问审计日志页面, THE Admin_UI SHALL 显示 AuditLog 列表，包含时间戳、操作用户、实体名称、操作类型和变更内容
2. WHEN 管理员按时间范围筛选审计日志, THE Admin_API SHALL 通过 Generic_CRUD_API 返回指定时间范围内的 AuditLog 记录
3. WHEN 管理员按实体名称或操作用户筛选审计日志, THE Admin_API SHALL 通过 Generic_CRUD_API 返回匹配条件的 AuditLog 记录
4. WHEN 审计日志数据超过单页容量, THE Admin_UI SHALL 提供分页导航，每页默认显示 50 条记录
5. THE Admin_UI SHALL 以只读方式展示审计日志，禁止任何修改或删除操作

### 需求 8：前端导航集成

**用户故事：** 作为系统管理员，我希望通过侧边栏导航快速访问系统管理的各个子功能，以便高效地进行管理操作。

#### 验收标准

1. WHEN 用户登录后, THE Admin_UI SHALL 在侧边栏导航中显示"系统管理"分组，包含用户管理、角色管理、系统设置和审计日志子项
2. WHEN 管理员点击导航子项, THE Admin_UI SHALL 路由到对应的管理页面
3. WHEN 当前页面属于系统管理模块, THE Admin_UI SHALL 高亮对应的导航子项

### 需求 9：后端服务安全性

**用户故事：** 作为系统架构师，我希望系统管理 API 遵循已建立的安全模式，以便保持架构一致性和安全性。

#### 验收标准

1. THE UserManagement_Service SHALL 使用 `ec.getArtifactExecution().disableAuthz()` 模式访问 Moqui 实体，并在 finally 块中恢复授权状态
2. THE SystemConfig_Service SHALL 使用 `ec.getArtifactExecution().disableAuthz()` 模式访问 Moqui 实体，并在 finally 块中恢复授权状态
3. WHEN 未携带有效 JWT Token 的请求访问 Admin_API, THE Admin_API SHALL 返回 401 未授权错误
4. THE Admin_API SHALL 对所有输入参数进行验证，拒绝包含非法字符的请求
