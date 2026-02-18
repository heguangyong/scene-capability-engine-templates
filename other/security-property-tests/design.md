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

# 设计文档：安全层属性测试

**Spec ID**: {{SPEC_NAME}}  
**版本**: 1.0  
**日期**: {{DATE}}

---

## 概述

本 Spec 为 Spec 17-00 实现的安全层编写 9 个 jqwik 属性测试。纯测试 Spec，不修改生产代码。

测试分为 3 个批次：
- **Batch A**（无 mock）：JWT 令牌属性 + RoutePermissionRegistry 属性
- **Batch B**（轻量 mock）：认证中间件属性 + 安全错误属性
- **Batch C**（重量 mock）：RBAC 中间件属性 + AuthService 属性

---

## 架构

### 测试文件结构

```
backend/app/src/test/java/org/moqui/rest/
├── JwtTokenPropertyTest.java              # P1, P4, P5
├── RoutePermissionRegistryPropertyTest.java # P7
├── AuthMiddlewarePropertyTest.java         # P2, P3
├── SecurityErrorPropertyTest.java          # P9
├── RbacMiddlewarePropertyTest.java         # P8
└── AuthServicePropertyTest.java            # P6
```

### Mock 策略

| 组件 | Mock 方式 | 说明 |
|------|----------|------|
| JwtUtil | 无需 mock | 静态方法，设置 `jwt.secret` 系统属性即可 |
| RoutePermissionRegistry | 无需 mock | 纯 Java 逻辑 |
| HttpServletRequest | 匿名内部类 | 实现 `header()`, `getMethod()`, `getRequestURI()`, `getRemoteAddr()` |
| HttpServletResponse | 匿名内部类 | 实现 `setStatus()`, `setContentType()`, `getOutputStream()` |
| RouteMatch | 直接构造 | `new RouteMatch(null, Map.of(), isPublic, pattern)` |
| ExecutionContextFactory | 匿名内部类 | 模拟 `getExecutionContext()` → mock EC → mock EntityFind |
| AuthService | 不直接测试 | P6 验证错误消息常量，不需要 Moqui 上下文 |

---

## 组件与接口

### 1. JwtTokenPropertyTest

直接调用 `JwtUtil` 静态方法，测试前设置系统属性：

```java
@BeforeEach / @BeforeProperty
void setup() {
    System.setProperty("jwt.secret", "test-secret-key-at-least-32-chars-long!");
    System.setProperty("jwt.issuer", "test-issuer");
}
```

测试 P1（往返一致性）、P4（刷新保持 userId）、P5（吊销后失败）。

### 2. AuthMiddlewarePropertyTest

需要构造 mock `RequestContext`：

```java
// 最小化 HttpServletRequest mock
private static HttpServletRequest mockRequest(String method, String path, String authHeader, String ip) {
    // 返回匿名类，实现 getMethod(), getRequestURI(), getHeader("Authorization"),
    // getRemoteAddr(), getHeader("X-Forwarded-For") 等方法
}

// 最小化 HttpServletResponse mock — 需要捕获 status 和 body
private static HttpServletResponse mockResponse() {
    // 返回匿名类，实现 setStatus(), setContentType(), getOutputStream()
}
```

测试 P2（公开端点跳过）、P3（无效 Auth 头拒绝）。

### 3. RoutePermissionRegistryPropertyTest

纯逻辑测试，无需 mock：

```java
// 生成随机 (method, pathPattern, permission) 三元组
// 注册后查询验证返回值，未注册组合验证返回 null
```

### 4. RbacMiddlewarePropertyTest

需要 mock `ExecutionContextFactory` 链：

```java
ECF → EC → EntityFacade → EntityFind → count()
```

通过控制 `count()` 返回值（0 或 1）模拟用户有/无权限。

### 5. SecurityErrorPropertyTest

直接调用 `ApiErrorHandler.mapException()` 和 `sanitizeErrorMessage()`，无需 mock。

### 6. AuthServicePropertyTest

P6 验证错误消息一致性。由于 AuthService 依赖 Moqui 上下文，直接验证 `AuthResult.error()` 的消息常量即可——检查源码中两个失败路径使用相同的错误消息字符串。

---

## 数据模型

### jqwik 生成器

| 生成器 | 类型 | 范围 |
|--------|------|------|
| `userId` | `String` | 字母数字，5-20 字符 |
| `clientIp` | `String` | IPv4 格式 `x.x.x.x`，每段 1-255 |
| `httpMethod` | `String` | `GET`, `POST`, `PUT`, `DELETE` 之一 |
| `pathPattern` | `String` | `/api/v1/` + 字母段，1-3 段 |
| `permission` | `String` | 大写字母 + 下划线，如 `ENTITY_VIEW` |
| `authHeader` | `String` | 不以 `"Bearer "` 开头的随机字符串 |
| `exceptionClass` | `String` | 随机 Java 类名或包含敏感模式的字符串 |
| `errorMessage` | `String` | 随机字符串，可能包含堆栈跟踪、SQL、文件路径 |

---

## 正确性属性

*属性（Property）是在系统所有合法执行中都应成立的特征或行为——本质上是对系统应做什么的形式化陈述。属性是人类可读规格与机器可验证正确性保证之间的桥梁。*

### Property 1: JWT 令牌签发-验证往返一致性

*For any* 合法的 userId（非空字母数字字符串）和 clientIp（合法 IPv4），`JwtUtil.generateTokenPair(userId, clientIp)` 签发的 Access_Token 经 `JwtUtil.validateToken(accessToken, clientIp)` 验证后，`result.isValid()` 为 true 且 `result.getUserId()` 等于原始 userId。

**Validates: Requirements 1.1**

### Property 2: 公开端点跳过认证

*For any* 标记为 `isPublic=true` 的 RouteMatch 和任意 Authorization 头值（包括 null、空字符串、有效 Bearer、无效字符串），`JwtAuthMiddleware.handle()` 返回 true。

**Validates: Requirements 2.1**

### Property 3: 无效 Authorization 头拒绝

*For any* 不以 `"Bearer "` 开头的 Authorization 头值（包括 null），对非公开路由调用 `JwtAuthMiddleware.handle()` 返回 false 且响应状态码为 401。

**Validates: Requirements 2.2**

### Property 4: 令牌刷新保持 userId 不变

*For any* 合法的 userId 和 clientIp，通过 `generateTokenPair()` 生成令牌对后，使用 Refresh_Token 调用 `refreshAccessToken()` 获得新 Access_Token，经 `validateToken()` 验证后提取的 userId 等于原始 userId。

**Validates: Requirements 1.2**

### Property 5: 令牌吊销后验证失败

*For any* 合法的 userId 和 clientIp，通过 `generateTokenPair()` 生成的 Access_Token，调用 `revokeToken()` 后，`validateToken()` 返回 `isValid() == false`。

**Validates: Requirements 1.3**

### Property 6: 登录失败消息一致性（示例测试）

验证 `AuthService` 源码中用户不存在和密码错误两个失败路径返回的错误消息字符串完全相同（均为 `"Invalid username or password"`）。

**Validates: Requirements 5.1**

### Property 7: RoutePermissionRegistry 查询正确性

*For any* 一组通过 `require(method, pathPattern, permission)` 注册的权限映射，`getRequiredPermission(method, pathPattern)` 返回注册时的 permission 值；对于未注册的 (method, pathPattern) 组合返回 null。

**Validates: Requirements 3.1, 3.2**

### Property 8: RBAC 权限授予/拒绝正确性

*For any* 已认证用户和受保护端点：当端点配置了权限要求且用户拥有该权限时 `RbacMiddleware.handle()` 返回 true；当用户缺少该权限时返回 false 且状态码为 403；当端点未配置权限要求时返回 true。

**Validates: Requirements 4.1, 4.2, 4.3**

### Property 9: 错误响应不泄露内部细节

*For any* 异常类名和错误消息字符串，`ApiErrorHandler.mapException(className, message, true)` 在生产模式下返回的响应不包含 Java 堆栈跟踪模式、完整限定异常类名、SQL 关键字或文件系统路径。

**Validates: Requirements 5.2**

---

## 错误处理

本 Spec 为纯测试 Spec，不涉及生产代码错误处理变更。

测试本身的错误处理：
- 每个 `@Property` 方法在 `@AfterEach` 中清理系统属性和 JwtUtil 状态
- Mock 对象在测试方法内创建，无需全局清理
- `JwtUtil.cleanupRevokedTokens()` 在 P5 测试后调用，避免内存泄漏

---

## 测试策略

### 框架

- **属性测试**: jqwik 1.9.0（已在 `pom.xml` 中配置）
- **单元测试**: JUnit 5
- **Mock**: 手动匿名内部类（无 Mockito 依赖）
- **位置**: `backend/app/src/test/java/org/moqui/rest/`

### 属性测试配置

- 每个 `@Property` 注解设置 `tries = 100`
- 每个测试方法注释格式：`// Feature: {{SPEC_NAME}}, Property N: {description}`
- 每个测试方法注释包含：`// Validates: Requirements X.Y`

### 执行批次

| 批次 | 测试类 | Properties | Mock 复杂度 |
|------|--------|-----------|------------|
| A | JwtTokenPropertyTest, RoutePermissionRegistryPropertyTest | P1, P4, P5, P7 | 无 |
| B | AuthMiddlewarePropertyTest, SecurityErrorPropertyTest | P2, P3, P9 | 轻量（HttpServlet mock） |
| C | RbacMiddlewarePropertyTest, AuthServicePropertyTest | P8, P6 | 重量（ECF mock 链） |

### 验证

- 每个批次完成后运行 `mvn test` 确认全部通过
- 最终确认总测试数从 190 增长到预期数量
