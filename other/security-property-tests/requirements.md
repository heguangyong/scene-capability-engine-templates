---
name: security-property-tests
category: other
description: Template for Security Property Tests
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：安全层属性测试

## 简介

本 Spec 为 Spec 17-00（JWT 认证与 RBAC 授权）实现的安全层编写 jqwik 属性测试（Property-Based Tests）。这是一个纯测试 Spec，不修改任何生产代码。通过 9 个属性测试覆盖 JWT 令牌生命周期、认证中间件、RBAC 授权中间件、路由权限注册表和安全错误处理的正确性。

## 术语表

- **JWT_Util**: `JwtUtil`，JWT 令牌签发、验证、吊销、刷新的工具类
- **Auth_Middleware**: `JwtAuthMiddleware`，从 HTTP 请求中提取并验证 JWT Bearer Token 的中间件
- **RBAC_Middleware**: `RbacMiddleware`，检查用户是否拥有端点所需权限的授权中间件
- **Route_Permission_Registry**: `RoutePermissionRegistry`，路由到所需权限的映射注册表
- **Auth_Service**: `AuthService`，封装认证业务逻辑的服务类
- **Api_Error_Handler**: `ApiErrorHandler`，将异常映射为安全错误响应的处理器
- **Property_Test**: 使用 jqwik `@Property` 注解的属性测试，对随机生成的输入验证通用属性

## 需求

### 需求 1：JWT 令牌属性测试

**用户故事：** 作为开发者，我希望通过属性测试验证 JWT 令牌的签发、验证、刷新和吊销逻辑，以确保令牌生命周期管理的正确性。

#### 验收标准

1. WHEN 使用任意合法 userId 和 clientIp 调用 `JwtUtil.generateTokenPair()` 后对返回的 Access_Token 调用 `JwtUtil.validateToken()`，THE Property_Test SHALL 验证提取的 userId 与签发时一致
2. WHEN 使用任意合法 userId 生成令牌对后使用 Refresh_Token 调用 `JwtUtil.refreshAccessToken()` 获得新 Access_Token，THE Property_Test SHALL 验证新令牌中的 userId 与原始 userId 一致
3. WHEN 对任意合法 JWT 令牌调用 `JwtUtil.revokeToken()` 后再调用 `JwtUtil.validateToken()`，THE Property_Test SHALL 验证返回结果为无效

### 需求 2：认证中间件属性测试

**用户故事：** 作为开发者，我希望通过属性测试验证 JwtAuthMiddleware 对公开路由和无效请求的处理逻辑，以确保认证层的正确性。

#### 验收标准

1. WHEN 对任意标记为 `isPublic=true` 的路由调用 `JwtAuthMiddleware.handle()`，THE Property_Test SHALL 验证无论请求是否携带 Authorization 头都返回 true
2. WHEN 对任意不以 `"Bearer "` 开头的 Authorization 头值调用 `JwtAuthMiddleware.handle()`，THE Property_Test SHALL 验证返回 false 且响应状态码为 401

### 需求 3：路由权限注册表属性测试

**用户故事：** 作为开发者，我希望通过属性测试验证 RoutePermissionRegistry 的注册和查询逻辑，以确保权限映射的正确性。

#### 验收标准

1. WHEN 通过 `require(method, pathPattern, permission)` 注册任意权限映射后调用 `getRequiredPermission(method, pathPattern)`，THE Property_Test SHALL 验证返回注册时的 permission 值
2. WHEN 对未注册的 (method, pathPattern) 组合调用 `getRequiredPermission()`，THE Property_Test SHALL 验证返回 null

### 需求 4：RBAC 授权中间件属性测试

**用户故事：** 作为开发者，我希望通过属性测试验证 RbacMiddleware 的权限授予和拒绝逻辑，以确保授权层的正确性。

#### 验收标准

1. WHEN 已认证用户拥有端点所需权限时调用 `RbacMiddleware.handle()`，THE Property_Test SHALL 验证返回 true
2. WHEN 已认证用户缺少端点所需权限时调用 `RbacMiddleware.handle()`，THE Property_Test SHALL 验证返回 false 且响应状态码为 403
3. WHEN 端点未配置权限要求时调用 `RbacMiddleware.handle()`，THE Property_Test SHALL 验证任何已认证用户均返回 true

### 需求 5：安全错误处理属性测试

**用户故事：** 作为开发者，我希望通过属性测试验证认证授权错误响应不泄露内部实现细节，以确保安全性。

#### 验收标准

1. WHEN 对任意无效凭证（用户不存在或密码错误）调用 `AuthService.login()`，THE Property_Test SHALL 验证返回的错误消息完全相同
2. WHEN 对任意异常类名和消息调用 `ApiErrorHandler.mapException()` 并启用生产模式，THE Property_Test SHALL 验证返回的错误响应不包含堆栈跟踪、内部类名或 SQL 片段

### 需求 6：测试基础设施

**用户故事：** 作为开发者，我希望所有属性测试自包含且不依赖 Moqui 运行时，以确保测试可在 CI 环境中独立运行。

#### 验收标准

1. THE Property_Test SHALL 使用 jqwik 1.9.0 的 `@Property(tries = 100)` 注解运行至少 100 次迭代
2. WHEN 测试需要 HttpServletRequest 或 HttpServletResponse 时，THE Property_Test SHALL 使用手动实现的 mock 类而非外部 mock 框架
3. WHEN 测试需要 ExecutionContextFactory 时，THE Property_Test SHALL 使用手动实现的 mock 类模拟权限查询
4. THE Property_Test SHALL 通过 `mvn test` 命令与现有 190 个测试一起运行并全部通过
