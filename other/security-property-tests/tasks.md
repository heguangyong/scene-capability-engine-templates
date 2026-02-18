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

# Implementation Plan: 安全层属性测试

## Overview

为 Spec 17-00 安全层编写 9 个 jqwik 属性测试，分 3 个批次递增实现。纯测试 Spec，不修改生产代码。

## Tasks

- [x] 1. Batch A — JWT 令牌属性测试（无 mock）
  - [x] 1.1 创建 `JwtTokenPropertyTest.java`
    - 设置 `jwt.secret` 系统属性（`@BeforeEach`/`@AfterEach`）
    - 实现 userId 和 clientIp 的 jqwik `@Provide` 生成器
    - _Requirements: 1.1, 1.2, 1.3_
  - [x]* 1.2 实现 Property 1: JWT 签发-验证往返一致性
    - **Property 1: JWT 令牌签发-验证往返一致性**
    - `generateTokenPair(userId, clientIp)` → `validateToken(accessToken, clientIp)` → userId 一致
    - **Validates: Requirements 1.1**
  - [x]* 1.3 实现 Property 4: 令牌刷新保持 userId 不变
    - **Property 4: 令牌刷新保持 userId 不变**
    - `generateTokenPair()` → `refreshAccessToken(refreshToken, clientIp)` → `validateToken()` → userId 一致
    - **Validates: Requirements 1.2**
  - [x]* 1.4 实现 Property 5: 令牌吊销后验证失败
    - **Property 5: 令牌吊销后验证失败**
    - `generateTokenPair()` → `revokeToken(accessToken)` → `validateToken()` → isValid == false
    - 测试后调用 `cleanupRevokedTokens()`
    - **Validates: Requirements 1.3**

- [x] 2. Batch A — RoutePermissionRegistry 属性测试（无 mock）
  - [x] 2.1 创建 `RoutePermissionRegistryPropertyTest.java`
    - 实现 httpMethod、pathPattern、permission 的 jqwik 生成器
    - _Requirements: 3.1, 3.2_
  - [x]* 2.2 实现 Property 7: RoutePermissionRegistry 查询正确性
    - **Property 7: RoutePermissionRegistry 查询正确性**
    - 注册随机 (method, pathPattern, permission) → `getRequiredPermission()` 返回正确值；未注册组合返回 null
    - **Validates: Requirements 3.1, 3.2**

- [x] 3. Checkpoint — Batch A 验证
  - 运行 `mvn test`，确保 Batch A 测试全部通过，ask the user if questions arise.

- [x] 4. Batch B — 认证中间件属性测试（轻量 mock）
  - [x] 4.1 创建 `AuthMiddlewarePropertyTest.java`
    - 实现 HttpServletRequest/Response mock 辅助方法（匿名内部类）
    - 实现 RequestContext 和 RouteMatch 构造辅助方法
    - _Requirements: 2.1, 2.2_
  - [x]* 4.2 实现 Property 2: 公开端点跳过认证
    - **Property 2: 公开端点跳过认证**
    - 任意 `isPublic=true` 的 RouteMatch + 任意 Authorization 头 → `handle()` 返回 true
    - **Validates: Requirements 2.1**
  - [x]* 4.3 实现 Property 3: 无效 Authorization 头拒绝
    - **Property 3: 无效 Authorization 头拒绝**
    - 任意不以 `"Bearer "` 开头的 Authorization 头 + 非公开路由 → `handle()` 返回 false，状态码 401
    - **Validates: Requirements 2.2**

- [x] 5. Batch B — 安全错误属性测试（无 mock）
  - [x] 5.1 创建 `SecurityErrorPropertyTest.java`
    - 实现异常类名和错误消息的 jqwik 生成器（包含堆栈跟踪、SQL、文件路径等敏感模式）
    - _Requirements: 5.2_
  - [x]* 5.2 实现 Property 9: 错误响应不泄露内部细节
    - **Property 9: 错误响应不泄露内部细节**
    - 任意异常类名和消息 → `mapException(className, message, true)` 返回的响应不含堆栈跟踪、类名、SQL、文件路径
    - **Validates: Requirements 5.2**

- [x] 6. Checkpoint — Batch B 验证
  - 运行 `mvn test`，确保 Batch A + B 测试全部通过，ask the user if questions arise.

- [x] 7. Batch C — RBAC 中间件属性测试（重量 mock）
  - [x] 7.1 创建 `RbacMiddlewarePropertyTest.java`
    - 实现 ECF → EC → EntityFacade → EntityFind mock 链（匿名内部类）
    - 通过控制 `count()` 返回值模拟用户有/无权限
    - 实现 RequestContext 和 RouteMatch 构造辅助方法
    - _Requirements: 4.1, 4.2, 4.3_
  - [x]* 7.2 实现 Property 8: RBAC 权限授予/拒绝正确性
    - **Property 8: RBAC 权限授予/拒绝正确性**
    - 有权限 → handle() 返回 true；无权限 → 返回 false + 403；无权限要求 → 返回 true
    - **Validates: Requirements 4.1, 4.2, 4.3**

- [x] 8. Batch C — AuthService 登录失败消息一致性（示例测试）
  - [x] 8.1 创建 `AuthServicePropertyTest.java`
    - _Requirements: 5.1_
  - [x]* 8.2 实现 Property 6: 登录失败消息一致性
    - **Property 6: 登录失败消息一致性**
    - 验证 AuthService 源码中两个失败路径（用户不存在、密码错误）使用相同的错误消息常量
    - **Validates: Requirements 5.1**

- [x] 9. Final Checkpoint — 全量验证
  - 运行 `mvn test`，确保所有测试通过（原 190 + 新增属性测试）
  - 确认测试总数增长符合预期
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- 所有测试文件位于 `backend/app/src/test/java/org/moqui/rest/`
- 不修改任何生产代码
- 使用手动 mock（匿名内部类），不引入 Mockito
- 每个 `@Property` 设置 `tries = 100`
- {{DATE}} 全量 `mvn test` 在现有基线下仍有非本 Spec 相关失败：`ApiCatalogControllerWorkflowPropertyTest`（`/api/v1/search` vs `/api/v1/catalog/search`）、`EndpointRegressionPropertyTest`（`/api/v1/search` 不可路由）、`ServiceApiDocumentationPropertyTest`（catalog OpenAPI 通用查询参数断言失败）；本 Spec 新增 6 个安全属性测试类单独运行通过
