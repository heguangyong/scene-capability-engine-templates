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

# 实现计划：JWT 认证与 RBAC 授权

## 概述

在现有 JWT 认证基础设施上补全 `/me` 端点、新增 RBAC 授权中间件和路由权限注册表，并通过 jqwik 属性测试验证正确性。

## 任务

- [x] 1. AuthController 补全 /me 端点
  - [x] 1.1 在 `AuthController` 中新增 `me()` 方法和路由注册
    - 在 `register()` 中添加 `router.get("/api/v1/auth/me", this::me)`
    - `me()` 方法通过 `ctx.userId()` 查询 `UserAccount` 获取 username
    - 查询 `UserGroupMember` 获取用户所属组列表
    - 返回 `{ userId, username, groups: [...] }`
    - _Requirements: 2.7_
  - [x]* 1.2 编写 AuthController /me 端点单元测试
    - 测试已认证用户返回正确的 userId、username、groups
    - 测试未认证用户（userId 为 null）返回 401
    - _Requirements: 2.7_

- [x] 2. RoutePermissionRegistry 实现
  - [x] 2.1 创建 `RoutePermissionRegistry` 类
    - 文件: `backend/app/src/main/java/org/moqui/rest/RoutePermissionRegistry.java`
    - 实现 `require(method, pathPattern, permission)` 注册方法
    - 实现 `requireCrud(pathPattern, viewPermission, adminPermission)` 便捷方法
    - 实现 `getRequiredPermission(method, routePattern)` 查询方法
    - 路径匹配基于 Router 已解析的 routePattern 精确匹配
    - _Requirements: 5.1, 5.2, 5.3_
  - [x]* 2.2 编写 RoutePermissionRegistry 属性测试
    - **Property 7: RoutePermissionRegistry 查询正确性**
    - **Validates: Requirements 5.1, 5.2, 5.3**
  - [x] 2.3 注册默认权限配置
    - 在应用启动时（路由注册处）配置默认权限映射
    - 实体端点: GET → `ENTITY_VIEW`, POST/PUT/DELETE → `ENTITY_ADMIN`
    - 服务端点: POST → `SERVICE_INVOKE`
    - _Requirements: 5.4, 5.5_

- [x] 3. RbacMiddleware 实现
  - [x] 3.1 创建 `RbacMiddleware` 类
    - 文件: `backend/app/src/main/java/org/moqui/rest/middleware/RbacMiddleware.java`
    - 实现 `Middleware` 接口
    - 公开路由跳过授权检查
    - 无权限要求的端点仅需认证通过
    - 通过 Moqui `UserPermissionCheck` 视图实体查询用户权限
    - 多组权限合并（实体查询自动处理）
    - 权限不足返回 403 `PERMISSION_DENIED`
    - 查询异常时记录日志并拒绝访问
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6_
  - [x]* 3.2 编写 RBAC 权限授予/拒绝属性测试
    - **Property 8: RBAC 权限授予/拒绝正确性**
    - 使用 mock ECF 和实体查询，生成随机用户权限集和端点权限要求
    - **Validates: Requirements 4.1, 4.2, 4.3, 4.4, 4.5**
  - [x] 3.3 将 RbacMiddleware 集成到 RestServer 流水线
    - 修改 `RestServer.buildPipeline()`，在 `JwtAuthMiddleware` 之后添加 `RbacMiddleware`
    - 传入 `RoutePermissionRegistry` 和 `ecf` 依赖
    - _Requirements: 4.1_

- [x] 4. Checkpoint — 确保所有测试通过
  - 运行 `mvn test`，确保现有 190 测试 + 新增测试全部通过
  - 如有问题请询问用户

- [x] 5. JWT 令牌属性测试
  - [x]* 5.1 编写 JWT 往返一致性属性测试
    - **Property 1: JWT 令牌签发-验证往返一致性**
    - 生成随机 userId 和 clientIp，验证 generateTokenPair → validateToken → userId 一致
    - **Validates: Requirements 1.1, 3.1, 3.2**
  - [x]* 5.2 编写令牌刷新保持 userId 属性测试
    - **Property 4: 令牌刷新保持 userId 不变**
    - 生成随机 userId，验证 generateTokenPair → refreshAccessToken → validateToken → userId 一致
    - **Validates: Requirements 2.4, 3.3**
  - [x]* 5.3 编写令牌吊销后验证失败属性测试
    - **Property 5: 令牌吊销后验证失败**
    - 生成随机令牌，revokeToken 后 validateToken 应返回无效
    - **Validates: Requirements 2.6, 3.4**

- [x] 6. Auth 中间件属性测试
  - [x]* 6.1 编写公开端点跳过认证属性测试
    - **Property 2: 公开端点跳过认证**
    - 生成随机公开路由，验证无论有无 token 都通过
    - **Validates: Requirements 1.5**
  - [x]* 6.2 编写无效 Auth 头拒绝属性测试
    - **Property 3: 无效 Authorization 头拒绝**
    - 生成随机非 Bearer 格式字符串，验证全部返回 401
    - **Validates: Requirements 1.2**

- [x] 7. 安全性属性测试
  - [x]* 7.1 编写登录失败消息一致性属性测试
    - **Property 6: 登录失败消息一致性**
    - 对比用户不存在和密码错误的错误消息应完全相同
    - **Validates: Requirements 2.2**
  - [x]* 7.2 编写错误响应不泄露内部细节属性测试
    - **Property 9: 错误响应不泄露内部细节**
    - 生成随机异常，验证错误响应不含堆栈跟踪或类名
    - **Validates: Requirements 6.4**

- [x] 8. Final checkpoint — 全量测试通过
  - 运行 `mvn test`，确保所有测试通过
  - 如有问题请询问用户

## 备注

- 标记 `*` 的子任务为可选测试任务，可跳过以加速 MVP
- 每个任务引用具体需求编号以确保可追溯性
- 属性测试验证通用正确性属性，单元测试验证具体示例和边界情况
- 实现语言: Java 21，测试框架: jqwik 1.9.0 + JUnit 5
