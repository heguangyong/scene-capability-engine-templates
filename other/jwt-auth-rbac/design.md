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

# 设计文档：JWT 认证与 RBAC 授权

**Spec ID**: {{SPEC_NAME}}  
**版本**: 1.0  
**日期**: {{DATE}}

---

## 概述

本设计在现有 JWT 认证基础设施（`JwtAuthMiddleware`、`AuthController`、`AuthService`、`JwtUtil`）之上，补全缺失功能并新增 RBAC 授权层。

**现有资产（已实现）**：
- `JwtAuthMiddleware` — Bearer Token 验证、公开路由跳过、userId 注入
- `AuthController` — login / refresh / logout 端点
- `AuthService` — 凭证校验（Moqui UserAccount）、令牌生成/刷新/吊销
- `JwtUtil` — JWT 签发/验证/吊销/刷新，支持 HMAC 和 RSA 算法

**本 Spec 新增**：
- `GET /api/v1/auth/me` 端点
- `RbacMiddleware` — 基于 Moqui 权限模型的授权中间件
- `RoutePermissionRegistry` — 路由到权限的映射注册表
- jqwik 属性测试覆盖

---

## 架构

### 请求处理流水线

```
HTTP Request
    │
    ▼
MetricsMiddleware → SecurityHeadersMiddleware → CorsMiddleware → RateLimitMiddleware
    │
    ▼
JwtAuthMiddleware (认证层)
    │  ├─ Public route? → 跳过，继续
    │  ├─ 有效 Bearer Token? → setUserId()，继续
    │  └─ 无效/缺失/过期? → 401，短路
    ▼
RbacMiddleware (授权层) ← 新增
    │  ├─ 路由无权限要求? → 继续
    │  ├─ 用户有所需权限? → 继续
    │  └─ 用户缺少权限? → 403，短路
    ▼
SanitizationMiddleware → Controller
```

### 中间件注册顺序

在 `RestServer.buildPipeline()` 中，`RbacMiddleware` 插入到 `JwtAuthMiddleware` 之后、`SanitizationMiddleware` 之前：

```java
private MiddlewarePipeline buildPipeline() {
    return new MiddlewarePipeline()
        .add(new MetricsMiddleware(metricsService))
        .add(new SecurityHeadersMiddleware())
        .add(new CorsMiddleware(config))
        .add(new RateLimitMiddleware(new RateLimiter(config.getRateLimitPerMinute())))
        .add(new JwtAuthMiddleware())
        .add(new RbacMiddleware(routePermissionRegistry, ecf))  // 新增
        .add(new SanitizationMiddleware(config));
}
```

---

## 组件与接口

### 1. AuthController 补全

新增 `GET /api/v1/auth/me` 端点：

```java
// AuthController.java — 新增方法
public void register(Router router) {
    router.publicRoute("POST", "/api/v1/auth/login", this::login);
    router.publicRoute("POST", "/api/v1/auth/refresh", this::refresh);
    router.post("/api/v1/auth/logout", this::logout);
    router.get("/api/v1/auth/me", this::me);  // 新增
}

private void me(RequestContext ctx) throws Exception {
    String userId = ctx.userId();
    // 查询 UserAccount 获取 username
    // 查询 UserGroupMember 获取用户组列表
    // 返回 { userId, username, groups: [...] }
}
```

### 2. RbacMiddleware（新增）

```java
public class RbacMiddleware implements Middleware {
    private final RoutePermissionRegistry registry;
    private final ExecutionContextFactory ecf;

    @Override
    public boolean handle(RequestContext ctx, RouteMatch route) throws Exception {
        // 1. 公开路由跳过
        if (route != null && route.isPublic()) return true;

        // 2. 查询路由所需权限
        String requiredPermission = registry.getRequiredPermission(
            ctx.method(), route.getRoutePattern());

        // 3. 无权限要求 → 仅需认证即可
        if (requiredPermission == null) return true;

        // 4. 查询用户权限
        String userId = ctx.userId();
        if (userId == null) {
            ctx.error(403, "PERMISSION_DENIED", "Access denied");
            return false;
        }

        // 5. 检查权限（通过 Moqui 实体查询）
        boolean hasPermission = checkUserPermission(userId, requiredPermission);
        if (!hasPermission) {
            logger.warn("Permission denied: userId={}, path={}, required={}",
                userId, ctx.path(), requiredPermission);
            ctx.error(403, "PERMISSION_DENIED", "Insufficient permissions");
            return false;
        }
        return true;
    }
}
```

### 3. RoutePermissionRegistry（新增）

```java
public class RoutePermissionRegistry {
    // 存储: (method, pathPattern) → requiredPermission
    private final List<PermissionEntry> entries = new ArrayList<>();

    public RoutePermissionRegistry require(String method, String pathPattern, String permission) {
        entries.add(new PermissionEntry(method.toUpperCase(), pathPattern, permission));
        return this;
    }

    // 便捷方法：按 HTTP 方法区分读写
    public RoutePermissionRegistry requireCrud(String pathPattern,
            String viewPermission, String adminPermission) {
        require("GET", pathPattern, viewPermission);
        require("POST", pathPattern, adminPermission);
        require("PUT", pathPattern, adminPermission);
        require("DELETE", pathPattern, adminPermission);
        return this;
    }

    public String getRequiredPermission(String method, String routePattern) {
        // 精确匹配 routePattern（Router 已解析好的模式）
        // 返回 null 表示无权限要求
    }
}
```

### 4. 权限查询服务

在 `RbacMiddleware` 内部通过 Moqui 实体查询用户权限：

```java
private boolean checkUserPermission(String userId, String requiredPermission) {
    ExecutionContext ec = ecf.getExecutionContext();
    try {
        // 查询 UserPermissionCheck 视图实体
        // 该视图关联 UserGroupMember + UserGroupPermission
        long count = ec.getEntity().find("moqui.security.UserPermissionCheck")
            .condition("userId", userId)
            .condition("userPermissionId", requiredPermission)
            .count();
        return count > 0;
    } finally {
        ec.destroy();
    }
}
```

---

## 数据模型

### Moqui 安全实体（已有，复用）

```
UserAccount
├── userId (PK)
├── username
├── currentPassword (hashed)
├── successiveFailedLogins
└── ...

UserGroupMember
├── userGroupId (PK)
├── userId (PK)
├── fromDate / thruDate
└── ...

UserGroupPermission
├── userGroupId (PK)
├── userPermissionId (PK)
├── fromDate / thruDate
└── ...

UserPermissionCheck (视图实体)
├── userId
├── userPermissionId
└── (关联 UserGroupMember + UserGroupPermission)
```

### JWT Token Claims 结构

```json
{
  "sub": "userId",
  "type": "access" | "refresh",
  "ip": "clientIp",
  "jti": "tokenId (UUID)",
  "iss": "moqui-rest-api",
  "iat": 1234567890,
  "exp": 1234571490
}
```

### RoutePermissionRegistry 数据结构

```java
record PermissionEntry(String method, String pathPattern, String permission) {}
```

默认权限配置：

| 路由模式 | GET 权限 | POST/PUT/DELETE 权限 |
|---------|---------|---------------------|
| `/api/v1/entities/{entityName}` | `ENTITY_VIEW` | `ENTITY_ADMIN` |
| `/api/v1/entities/{entityName}/{id}` | `ENTITY_VIEW` | `ENTITY_ADMIN` |
| `/api/v1/services/{serviceName}` | — | `SERVICE_INVOKE` |
| `/api/v1/auth/*` | — | — (仅需认证) |
| `/api/v1/catalog/**` | — | — (仅需认证) |



---

## 正确性属性

*属性（Property）是在系统所有合法执行中都应成立的特征或行为——本质上是对系统应做什么的形式化陈述。属性是人类可读规格与机器可验证正确性保证之间的桥梁。*

### Property 1: JWT 令牌签发-验证往返一致性

*For any* 合法的 userId 和 clientIp，`JwtUtil.generateTokenPair()` 签发的 Access_Token 和 Refresh_Token，经 `JwtUtil.validateToken()` 验证后，提取的 userId 应与签发时的 userId 一致。

**Validates: Requirements 1.1, 3.1, 3.2**

### Property 2: 公开端点跳过认证

*For any* 标记为 `isPublic=true` 的路由，无论请求是否携带 Authorization 头，`JwtAuthMiddleware.handle()` 都应返回 `true`（允许通过）。

**Validates: Requirements 1.5**

### Property 3: 无效 Authorization 头拒绝

*For any* 不以 `"Bearer "` 开头的 Authorization 头值（包括 null、空字符串、其他前缀），`JwtAuthMiddleware.handle()` 应返回 `false` 并设置 HTTP 401 响应。

**Validates: Requirements 1.2**

### Property 4: 令牌刷新保持 userId 不变

*For any* 合法的 userId，通过 `JwtUtil.generateTokenPair()` 生成令牌对后，使用 Refresh_Token 调用 `JwtUtil.refreshAccessToken()` 获得的新 Access_Token，经验证后提取的 userId 应与原始 userId 一致。

**Validates: Requirements 2.4, 3.3**

### Property 5: 令牌吊销后验证失败

*For any* 合法的 JWT 令牌，调用 `JwtUtil.revokeToken()` 后，再次调用 `JwtUtil.validateToken()` 应返回无效结果。

**Validates: Requirements 2.6, 3.4**

### Property 6: 登录失败消息一致性

*For any* 无效凭证（用户不存在或密码错误），`AuthService.login()` 返回的错误消息应完全相同，不泄露具体失败原因。

**Validates: Requirements 2.2**

### Property 7: RoutePermissionRegistry 查询正确性

*For any* 通过 `require(method, pathPattern, permission)` 注册的权限映射，调用 `getRequiredPermission(method, pathPattern)` 应返回注册时的 permission 值；对于未注册的 (method, pathPattern) 组合应返回 null。

**Validates: Requirements 5.1, 5.2, 5.3**

### Property 8: RBAC 权限授予/拒绝正确性

*For any* 用户和受保护端点，当端点配置了权限要求时：用户拥有该权限则 `RbacMiddleware` 允许通过，用户缺少该权限则返回 HTTP 403。当端点未配置权限要求时，任何已认证用户均可通过。用户属于多个组时，有效权限为所有组权限的并集。

**Validates: Requirements 4.1, 4.2, 4.3, 4.4, 4.5**

### Property 9: 错误响应不泄露内部细节

*For any* 认证或授权过程中的异常，返回的 JSON 错误响应应仅包含 `error.code` 和 `error.message` 字段，不包含堆栈跟踪、类名或内部实现细节。

**Validates: Requirements 6.4**

---

## 错误处理

### 认证错误

| 场景 | HTTP 状态码 | 错误码 | 说明 |
|------|-----------|--------|------|
| 缺失 Authorization 头 | 401 | `AUTHENTICATION_REQUIRED` | 未提供认证信息 |
| 格式错误的 Authorization 头 | 401 | `AUTHENTICATION_REQUIRED` | 非 Bearer 格式 |
| 令牌签名无效 | 401 | `AUTHENTICATION_REQUIRED` | 令牌被篡改 |
| 令牌已过期 | 401 | `TOKEN_EXPIRED` | 需要刷新令牌 |
| 令牌已吊销 | 401 | `AUTHENTICATION_REQUIRED` | 令牌已被登出 |
| 用户名/密码错误 | 401 | `AUTHENTICATION_REQUIRED` | 统一消息，不区分原因 |
| 账户锁定 | 401 | `ACCOUNT_LOCKED` | 连续失败 5 次 |

### 授权错误

| 场景 | HTTP 状态码 | 错误码 | 说明 |
|------|-----------|--------|------|
| 缺少所需权限 | 403 | `PERMISSION_DENIED` | 用户无端点所需权限 |
| 权限查询异常 | 403 | `PERMISSION_DENIED` | 安全降级，拒绝访问 |

### 输入验证错误

| 场景 | HTTP 状态码 | 错误码 | 说明 |
|------|-----------|--------|------|
| 登录缺少用户名/密码 | 400 | `VALIDATION_ERROR` | 必填字段缺失 |
| 刷新缺少 refreshToken | 400 | `VALIDATION_ERROR` | 必填字段缺失 |

---

## 测试策略

### 测试框架

- **属性测试**: jqwik 1.9.0（Java PBT 框架）
- **单元测试**: JUnit 5
- **测试位置**: `backend/app/src/test/java/org/moqui/rest/`

### 属性测试（Property-Based Testing）

每个正确性属性对应一个 jqwik `@Property` 测试，最少 100 次迭代。

测试标注格式：`Feature: {{SPEC_NAME}}, Property N: {property_text}`

| Property | 测试类 | 说明 |
|----------|--------|------|
| P1: JWT 往返一致性 | `JwtTokenPropertyTest` | 生成随机 userId/clientIp，验证签发→验证→提取的往返一致性 |
| P2: 公开端点跳过 | `AuthMiddlewarePropertyTest` | 生成随机公开路由，验证无论有无 token 都通过 |
| P3: 无效 Auth 头拒绝 | `AuthMiddlewarePropertyTest` | 生成随机非 Bearer 格式字符串，验证全部被拒绝 |
| P4: 刷新保持 userId | `JwtTokenPropertyTest` | 生成随机 userId，验证刷新后 userId 不变 |
| P5: 吊销后验证失败 | `JwtTokenPropertyTest` | 生成随机令牌，吊销后验证应失败 |
| P6: 登录失败消息一致 | `AuthServicePropertyTest` | 对比用户不存在和密码错误的错误消息 |
| P7: Registry 查询正确性 | `RoutePermissionRegistryPropertyTest` | 生成随机注册条目，验证查询结果正确 |
| P8: RBAC 授予/拒绝 | `RbacMiddlewarePropertyTest` | 生成随机用户权限集和端点权限要求，验证授予/拒绝逻辑 |
| P9: 错误响应安全 | `SecurityErrorPropertyTest` | 生成随机异常，验证响应不含内部细节 |

### 单元测试

单元测试覆盖具体示例和边界情况：

- `AuthControllerTest` — /me 端点返回正确用户信息、登录锁定阈值（5 次）
- `RbacMiddlewareTest` — 默认权限配置（ENTITY_VIEW、ENTITY_ADMIN、SERVICE_INVOKE）
- `RoutePermissionRegistryTest` — requireCrud 便捷方法、默认配置验证

### 测试隔离

- JWT 相关测试直接调用 `JwtUtil` 静态方法，无需 Moqui 上下文
- RBAC 权限查询测试需要 mock `ExecutionContextFactory` 和实体查询
- AuthService 测试需要 mock Moqui `UserAccount` 实体操作
