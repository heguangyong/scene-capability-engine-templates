---
name: jwt-auth-rbac
category: other
description: Template for Jwt Auth Rbac
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：JWT 认证与 RBAC 授权

## 简介

本 Spec 为 331-poc Moqui ERP 现代化项目落地安全能力：JWT 认证中间件加固、Auth API 补全、RBAC 授权中间件实现，以及配套的属性测试。当前项目已有 `JwtAuthMiddleware`、`AuthController`、`AuthService`、`JwtUtil` 等基础实现，本 Spec 在此基础上补全缺失功能、增加 RBAC 层、并通过 jqwik 属性测试验证正确性。

## 术语表

- **Auth_Middleware**: `JwtAuthMiddleware`，负责从 HTTP 请求中提取并验证 JWT Bearer Token 的中间件
- **RBAC_Middleware**: `RbacMiddleware`，在认证通过后检查用户是否拥有访问当前端点所需权限的中间件
- **Auth_Controller**: `AuthController`，处理登录、刷新、登出、获取当前用户信息的 REST 控制器
- **Auth_Service**: `AuthService`，封装认证业务逻辑（凭证校验、令牌生成、令牌吊销）的服务类
- **JWT_Util**: `JwtUtil`，JWT 令牌签发、验证、吊销的工具类（位于 framework 模块）
- **Access_Token**: 短期有效的 JWT 访问令牌，用于 API 请求鉴权
- **Refresh_Token**: 长期有效的 JWT 刷新令牌，用于换发新的 Access_Token
- **Public_Endpoint**: 无需认证即可访问的端点（如 `/docs`、`/health`、`/api/v1/auth/login`）
- **Protected_Endpoint**: 需要有效 JWT 才能访问的端点
- **Permission**: Moqui 权限标识符，存储于 `UserGroupPermission` 实体
- **User_Group**: Moqui 用户组，通过 `UserGroupMember` 关联用户
- **Route_Permission_Map**: 路由到所需权限的映射配置

## 需求

### 需求 1：JWT 认证中间件加固

**用户故事：** 作为 API 消费者，我希望所有受保护端点都经过 JWT 认证，以确保只有合法用户能访问系统资源。

#### 验收标准

1. WHEN 请求携带有效的 Bearer Token 访问 Protected_Endpoint，THE Auth_Middleware SHALL 验证令牌签名和有效期，并将 userId 设置到 RequestContext 中
2. WHEN 请求未携带 Authorization 头或格式不正确，THE Auth_Middleware SHALL 返回 HTTP 401 响应，错误码为 `AUTHENTICATION_REQUIRED`
3. WHEN 请求携带已过期的 Access_Token，THE Auth_Middleware SHALL 返回 HTTP 401 响应，错误码为 `TOKEN_EXPIRED`
4. WHEN 请求携带已被吊销的令牌，THE Auth_Middleware SHALL 返回 HTTP 401 响应，错误码为 `AUTHENTICATION_REQUIRED`
5. WHEN 请求访问 Public_Endpoint（`/docs`、`/redoc`、`/openapi.json`、`/health`、`/ready`、`/metrics`、`/api/v1/auth/login`、`/api/v1/auth/refresh`），THE Auth_Middleware SHALL 跳过认证检查并允许请求通过

### 需求 2：Auth API 补全

**用户故事：** 作为前端开发者，我希望有完整的认证 API，以便实现登录、令牌刷新、登出和获取当前用户信息的功能。

#### 验收标准

1. WHEN 用户提交有效的用户名和密码到 `POST /api/v1/auth/login`，THE Auth_Controller SHALL 返回包含 accessToken、refreshToken、expiresIn 和 userId 的成功响应
2. WHEN 用户提交无效的凭证到 `POST /api/v1/auth/login`，THE Auth_Controller SHALL 返回 HTTP 401 响应，不泄露具体失败原因（用户不存在或密码错误统一返回相同消息）
3. WHEN 用户连续登录失败达到 5 次，THE Auth_Service SHALL 锁定该账户并返回 `ACCOUNT_LOCKED` 错误码
4. WHEN 用户提交有效的 Refresh_Token 到 `POST /api/v1/auth/refresh`，THE Auth_Controller SHALL 返回新的 accessToken 和 refreshToken
5. WHEN 用户提交无效或过期的 Refresh_Token 到 `POST /api/v1/auth/refresh`，THE Auth_Controller SHALL 返回 HTTP 401 响应，错误码为 `TOKEN_EXPIRED`
6. WHEN 已认证用户请求 `POST /api/v1/auth/logout`，THE Auth_Controller SHALL 吊销当前令牌并返回成功响应
7. WHEN 已认证用户请求 `GET /api/v1/auth/me`，THE Auth_Controller SHALL 返回当前用户的 userId、username 和所属用户组列表

### 需求 3：JWT 令牌生命周期管理

**用户故事：** 作为系统管理员，我希望 JWT 令牌有合理的生命周期管理，以平衡安全性和用户体验。

#### 验收标准

1. THE JWT_Util SHALL 生成包含 userId、tokenType、clientIp 和 tokenId 声明的 Access_Token
2. THE JWT_Util SHALL 生成包含 userId、tokenType、clientIp 和 tokenId 声明的 Refresh_Token
3. WHEN Access_Token 过期后使用 Refresh_Token 换发新令牌，THE JWT_Util SHALL 生成新的 Access_Token 并保持 userId 不变
4. WHEN 令牌被吊销后再次使用，THE JWT_Util SHALL 拒绝该令牌的验证请求
5. THE JWT_Util SHALL 通过 `application.properties` 配置令牌有效期（access 默认 60 分钟，refresh 默认 30 天）

### 需求 4：RBAC 授权中间件

**用户故事：** 作为系统管理员，我希望不同角色的用户只能访问其权限范围内的 API 端点，以实现最小权限原则。

#### 验收标准

1. WHEN 已认证用户访问需要特定权限的 Protected_Endpoint，THE RBAC_Middleware SHALL 查询该用户所属 User_Group 的权限列表
2. WHEN 用户拥有端点所需的 Permission，THE RBAC_Middleware SHALL 允许请求继续执行
3. WHEN 用户缺少端点所需的 Permission，THE RBAC_Middleware SHALL 返回 HTTP 403 响应，错误码为 `PERMISSION_DENIED`
4. WHEN Protected_Endpoint 未配置权限要求（Route_Permission_Map 中无对应条目），THE RBAC_Middleware SHALL 仅要求认证通过即允许访问
5. WHEN 用户属于多个 User_Group，THE RBAC_Middleware SHALL 合并所有组的权限进行检查
6. IF RBAC_Middleware 查询权限时发生异常，THEN THE RBAC_Middleware SHALL 记录错误日志并拒绝访问（返回 HTTP 403）

### 需求 5：路由权限配置

**用户故事：** 作为开发者，我希望通过简洁的方式配置端点所需的权限，以便维护和扩展。

#### 验收标准

1. THE Route_Permission_Map SHALL 支持通过 Java 代码注册路由与所需权限的映射关系
2. THE Route_Permission_Map SHALL 支持路径模式匹配（如 `/api/v1/entities/{entityName}` 匹配所有实体端点）
3. WHEN 注册路由权限映射时，THE Route_Permission_Map SHALL 支持按 HTTP 方法区分权限（GET 读权限、POST/PUT/DELETE 写权限）
4. THE Route_Permission_Map SHALL 为实体 CRUD 端点默认要求 `ENTITY_VIEW`（GET）和 `ENTITY_ADMIN`（POST/PUT/DELETE）权限
5. THE Route_Permission_Map SHALL 为服务调用端点默认要求 `SERVICE_INVOKE` 权限

### 需求 6：安全审计与错误处理

**用户故事：** 作为安全审计员，我希望所有认证和授权事件都被记录，以便事后审计和问题排查。

#### 验收标准

1. WHEN 登录成功或失败，THE Auth_Service SHALL 记录包含用户名、客户端 IP 和结果的日志
2. WHEN 令牌验证失败，THE Auth_Middleware SHALL 记录包含请求路径和失败原因的 DEBUG 级别日志
3. WHEN 权限检查失败，THE RBAC_Middleware SHALL 记录包含 userId、请求路径和缺失权限的 WARN 级别日志
4. IF 认证或授权过程中发生未预期异常，THEN THE Auth_Middleware 或 RBAC_Middleware SHALL 返回通用错误响应，不泄露内部实现细节
