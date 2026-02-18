---
name: e2e-integration-tests
category: other
description: Template for E2e Integration Tests
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

端到端集成测试 Spec — 使用 embedded Jetty 启动真实 REST 服务器，通过 `java.net.http.HttpClient` 发送真实 HTTP 请求，验证完整的中间件链（Metrics → SecurityHeaders → CORS → RateLimit → JwtAuth → RBAC → Sanitization）和 API 端点行为。

本 Spec 为纯测试 Spec，不修改任何生产代码。测试目标是验证 HTTP 层的端到端行为，包括路由匹配、中间件链执行顺序、安全头注入、CORS 预检、JWT 认证流程、速率限制和标准错误响应格式。

由于完整的 Moqui ECFI 运行时过于重量级，测试采用"轻量级测试服务器"策略：构建一个最小化的 `RestServer` 实例，注册 mock handler 的路由，使用真实的中间件链，从而在不依赖 Moqui 运行时的情况下验证 HTTP 层行为。

## Glossary

- **Test_Server**: 在测试中启动的嵌入式 Jetty REST 服务器实例，使用 `RestServer` 类构建，配置测试专用端口和 mock ECF
- **HTTP_Client**: `java.net.http.HttpClient`，Java 标准库 HTTP 客户端，用于向 Test_Server 发送真实 HTTP 请求
- **Middleware_Pipeline**: 中间件流水线，按顺序执行 Metrics → SecurityHeaders → CORS → RateLimit → JwtAuth → RBAC → Sanitization
- **Security_Headers**: 安全响应头集合，包括 X-Content-Type-Options、X-Frame-Options、X-XSS-Protection、Cache-Control、Pragma
- **CORS_Headers**: 跨域资源共享响应头，包括 Access-Control-Allow-Origin、Access-Control-Allow-Methods、Access-Control-Allow-Headers、Access-Control-Max-Age
- **JWT_Token**: 由 `JwtUtil` 生成的 JSON Web Token，用于 Bearer 认证
- **Error_Response**: 标准错误响应格式 `{success: false, error: {code, message, httpStatus}}`
- **Public_Route**: 通过 `router.publicRoute()` 注册的路由，跳过 JWT 认证和 RBAC 检查
- **Protected_Route**: 非 public 路由，需要有效 JWT Token 才能访问
- **Rate_Limiter**: 滑动窗口速率限制器，按客户端 key（userId 或 IP）限制每分钟请求数

## Requirements

### Requirement 1: 测试服务器生命周期管理

**User Story:** As a 测试开发者, I want 在测试中启动和停止嵌入式 Jetty 服务器, so that 每个测试类拥有独立的服务器实例进行端到端验证。

#### Acceptance Criteria

1. WHEN a test class starts, THE Test_Server SHALL start an embedded Jetty server on a random available port within 5 seconds
2. WHEN a test class finishes, THE Test_Server SHALL stop the embedded Jetty server and release all resources
3. WHEN the Test_Server is running, THE HTTP_Client SHALL be able to connect and receive responses
4. THE Test_Server SHALL use a mock ExecutionContextFactory to avoid dependency on Moqui runtime

### Requirement 2: 安全响应头验证

**User Story:** As a 安全工程师, I want 验证所有 HTTP 响应都包含安全头, so that 确认 SecurityHeadersMiddleware 在中间件链中正确执行。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a GET request to a public route, THE Test_Server SHALL include X-Content-Type-Options header with value "nosniff" in the response
2. WHEN the HTTP_Client sends a GET request to a public route, THE Test_Server SHALL include X-Frame-Options header with value "DENY" in the response
3. WHEN the HTTP_Client sends a GET request to a public route, THE Test_Server SHALL include X-XSS-Protection header with value "1; mode=block" in the response
4. WHEN the HTTP_Client sends a GET request to a public route, THE Test_Server SHALL include Cache-Control header with value "no-store, no-cache, must-revalidate" in the response
5. WHEN the HTTP_Client sends a GET request to a public route, THE Test_Server SHALL include Pragma header with value "no-cache" in the response
6. WHEN the HTTP_Client sends a request that results in an error (401, 404, etc.), THE Test_Server SHALL still include all Security_Headers in the response

### Requirement 3: CORS 预检请求处理

**User Story:** As a 前端开发者, I want 验证 CORS 预检请求返回正确的跨域头, so that 确认浏览器跨域请求能正常工作。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends an OPTIONS preflight request to any route, THE Test_Server SHALL respond with HTTP status 204
2. WHEN the HTTP_Client sends an OPTIONS preflight request, THE Test_Server SHALL include Access-Control-Allow-Origin header matching the configured allowed origins
3. WHEN the HTTP_Client sends an OPTIONS preflight request, THE Test_Server SHALL include Access-Control-Allow-Methods header containing "GET, POST, PUT, DELETE, OPTIONS"
4. WHEN the HTTP_Client sends an OPTIONS preflight request, THE Test_Server SHALL include Access-Control-Allow-Headers header containing "Content-Type, Authorization, X-Async"
5. WHEN the HTTP_Client sends an OPTIONS preflight request, THE Test_Server SHALL include Access-Control-Max-Age header with value "3600"
6. WHEN the HTTP_Client sends a non-OPTIONS request, THE Test_Server SHALL include CORS_Headers in the response alongside Security_Headers

### Requirement 4: JWT 认证流程验证

**User Story:** As a 安全工程师, I want 验证 JWT 认证中间件正确拦截和验证请求, so that 确认未认证请求被拒绝、已认证请求被放行。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a request to a Protected_Route without Authorization header, THE Test_Server SHALL respond with HTTP 401 and error code "AUTHENTICATION_REQUIRED"
2. WHEN the HTTP_Client sends a request to a Protected_Route with an invalid JWT_Token, THE Test_Server SHALL respond with HTTP 401
3. WHEN the HTTP_Client sends a request to a Protected_Route with a valid JWT_Token in Bearer format, THE Test_Server SHALL execute the route handler and return a successful response
4. WHEN the HTTP_Client sends a request to a Protected_Route with an expired JWT_Token, THE Test_Server SHALL respond with HTTP 401 and error code "TOKEN_EXPIRED"
5. WHEN the HTTP_Client sends a request to a Protected_Route with Authorization header missing "Bearer " prefix, THE Test_Server SHALL respond with HTTP 401 and error code "AUTHENTICATION_REQUIRED"
6. WHEN the HTTP_Client sends a request to a Public_Route without Authorization header, THE Test_Server SHALL execute the route handler and return a successful response

### Requirement 5: 路由匹配与错误处理

**User Story:** As a API 开发者, I want 验证路由匹配和 HTTP 错误处理行为, so that 确认 404 和 405 响应格式正确。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a request to a non-existent path, THE Test_Server SHALL respond with HTTP 404 and error code "RESOURCE_NOT_FOUND"
2. WHEN the HTTP_Client sends a request with an unsupported HTTP method to an existing path, THE Test_Server SHALL respond with HTTP 405 and error code "METHOD_NOT_ALLOWED"
3. WHEN the Test_Server returns HTTP 405, THE Test_Server SHALL include an Allow header listing the supported methods for that path
4. THE Test_Server SHALL return all error responses in the standard Error_Response format with fields success, error.code, error.message, and error.httpStatus

### Requirement 6: 速率限制验证

**User Story:** As a 运维工程师, I want 验证速率限制中间件正确限制过多请求, so that 确认 API 受到保护不被滥用。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends requests exceeding the configured rate limit from the same IP, THE Test_Server SHALL respond with HTTP 429 and error code "RATE_LIMIT_EXCEEDED"
2. WHEN the Test_Server returns HTTP 429, THE Test_Server SHALL include a Retry-After header with value "60"
3. WHEN the HTTP_Client sends requests within the configured rate limit, THE Test_Server SHALL process all requests normally

### Requirement 7: 公开端点可访问性

**User Story:** As a API 消费者, I want 验证公开端点无需认证即可访问, so that 确认健康检查和文档端点始终可用。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a GET request to a registered Public_Route, THE Test_Server SHALL respond with HTTP 200 and the handler's response body
2. WHEN the HTTP_Client sends a GET request to a Public_Route, THE Test_Server SHALL not require an Authorization header
3. WHEN the HTTP_Client sends a GET request to a Public_Route, THE Test_Server SHALL still apply Security_Headers and CORS_Headers to the response

### Requirement 8: 标准错误响应格式一致性

**User Story:** As a 前端开发者, I want 验证所有错误响应遵循统一的 JSON 格式, so that 前端可以用统一的逻辑处理所有错误。

#### Acceptance Criteria

1. THE Test_Server SHALL return all error responses with Content-Type "application/json; charset=utf-8"
2. THE Test_Server SHALL return all error responses with JSON body containing field "success" set to false
3. THE Test_Server SHALL return all error responses with JSON body containing nested "error" object with fields "code" (string), "message" (string), and "httpStatus" (integer)
4. WHEN the Test_Server returns HTTP 401, THE Error_Response SHALL contain error code "AUTHENTICATION_REQUIRED" or "TOKEN_EXPIRED"
5. WHEN the Test_Server returns HTTP 404, THE Error_Response SHALL contain error code "RESOURCE_NOT_FOUND"
6. WHEN the Test_Server returns HTTP 405, THE Error_Response SHALL contain error code "METHOD_NOT_ALLOWED"
7. WHEN the Test_Server returns HTTP 429, THE Error_Response SHALL contain error code "RATE_LIMIT_EXCEEDED"

### Requirement 9: 中间件链执行顺序验证

**User Story:** As a 架构师, I want 验证中间件按正确顺序执行, so that 确认安全头在认证之前设置、速率限制在认证之前检查。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a request that triggers rate limiting, THE Test_Server SHALL return HTTP 429 with Security_Headers present (confirming SecurityHeaders runs before RateLimit)
2. WHEN the HTTP_Client sends an unauthenticated request to a Protected_Route, THE Test_Server SHALL return HTTP 401 with Security_Headers and CORS_Headers present (confirming SecurityHeaders and CORS run before JwtAuth)
3. WHEN the HTTP_Client sends an OPTIONS request to a Protected_Route, THE Test_Server SHALL return HTTP 204 without triggering JWT authentication (confirming CORS short-circuits before JwtAuth)

### Requirement 10: 请求体大小限制验证

**User Story:** As a 安全工程师, I want 验证过大的请求体被拒绝, so that 确认 SanitizationMiddleware 正确保护服务器。

#### Acceptance Criteria

1. WHEN the HTTP_Client sends a request with Content-Length exceeding the configured maxPayloadBytes, THE Test_Server SHALL respond with HTTP 413 and error code "PAYLOAD_TOO_LARGE"
2. WHEN the HTTP_Client sends a request with Content-Length within the configured maxPayloadBytes, THE Test_Server SHALL process the request normally
