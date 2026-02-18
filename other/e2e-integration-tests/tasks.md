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

# Implementation Plan: E2E Integration Tests

## Overview

纯测试 Spec — 在 `backend/app/src/test/java/org/moqui/rest/e2e/` 下创建端到端集成测试，使用嵌入式 Jetty + java.net.http.HttpClient 验证完整中间件链和 API 端点行为。不修改任何生产代码。

## Tasks

- [x] 1. Create E2eTestBase abstract base class
  - [x] 1.1 Create `E2eTestBase.java` in `backend/app/src/test/java/org/moqui/rest/e2e/`
    - Implement `findAvailablePort()` using `ServerSocket(0)`
    - Create `RestConfig` with test values (localhost, random port, rateLimitPerMinute=5, maxPayloadBytes=1024)
    - Create `RestServer` with null ECF and empty `RoutePermissionRegistry`
    - Register default public and protected test routes via `registerRoutes(Router)` hook
    - Start server in `@BeforeAll`, stop in `@AfterAll`
    - Create `HttpClient` with 5s timeout
    - Implement HTTP helper methods: `get()`, `post()`, `options()`, `sendRequest()`
    - Implement `parseJson()` using Jackson ObjectMapper
    - Implement assertion helpers: `assertSecurityHeaders()`, `assertCorsHeaders()`, `assertErrorResponse()`
    - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [x] 2. Implement Security Headers and CORS tests
  - [x] 2.1 Create `SecurityHeadersE2eTest.java`
    - Test all 5 security headers present on public route GET response
    - Test security headers present on 404 error response
    - Test security headers present on 401 error response
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6_
  - [x] 2.2 Create `CorsE2eTest.java`
    - Test OPTIONS preflight returns 204 with all CORS headers
    - Test CORS headers present on non-OPTIONS responses
    - Test OPTIONS on protected route returns 204 (no JWT required)
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6_

- [x] 3. Implement JWT Auth and Public Route tests
  - [x] 3.1 Create `JwtAuthE2eTest.java`
    - Test protected route without Authorization header → 401 AUTHENTICATION_REQUIRED
    - Test protected route with invalid JWT → 401
    - Test protected route with valid JWT → 200 success
    - Test protected route with malformed Authorization header (no "Bearer " prefix) → 401
    - Test public route without auth → 200 success
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6_
  - [x] 3.2 Create `PublicRouteE2eTest.java`
    - Test public route returns 200 with handler response body
    - Test public route includes security headers and CORS headers
    - _Requirements: 7.1, 7.2, 7.3_

- [x] 4. Implement Routing, Error Format, and Middleware Order tests
  - [x] 4.1 Create `RoutingE2eTest.java`
    - Test non-existent path → 404 RESOURCE_NOT_FOUND
    - Test wrong HTTP method → 405 METHOD_NOT_ALLOWED with Allow header
    - _Requirements: 5.1, 5.2, 5.3_
  - [x] 4.2 Create `ErrorFormatE2eTest.java`
    - Test 401 response has standard error format with correct Content-Type
    - Test 404 response has standard error format
    - Test 405 response has standard error format
    - Test 429 response has standard error format
    - Verify all error responses have success=false, error.code, error.message, error.httpStatus
    - _Requirements: 8.1, 8.2, 8.3, 8.4, 8.5, 8.6, 8.7_
  - [x] 4.3 Create `MiddlewareOrderE2eTest.java`
    - Test rate-limited response (429) includes security headers
    - Test unauthenticated response (401) includes security headers and CORS headers
    - Test OPTIONS on protected route returns 204 (CORS before JwtAuth)
    - _Requirements: 9.1, 9.2, 9.3_

- [x] 5. Implement Rate Limit and Payload Size tests
  - [x] 5.1 Create `RateLimitE2eTest.java`
    - Configure server with rateLimitPerMinute=5
    - Send 6 requests, verify 6th returns 429 with Retry-After header
    - Verify first 5 requests succeed normally
    - _Requirements: 6.1, 6.2, 6.3_
  - [x] 5.2 Create `PayloadSizeE2eTest.java`
    - Configure server with maxPayloadBytes=1024
    - Test POST with body > 1024 bytes → 413 PAYLOAD_TOO_LARGE
    - Test POST with body <= 1024 bytes → normal processing
    - _Requirements: 10.1, 10.2_

- [x] 6. Checkpoint - Ensure all example-based tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [x] 7. Implement Property-Based E2E tests
  - [x]* 7.1 Write property test: Security headers invariant
    - **Property 1: Security headers invariant**
    - Generate random HTTP methods and paths, verify all 5 security headers present on every response
    - **Validates: Requirements 2.1, 2.2, 2.3, 2.4, 2.5, 2.6**
  - [x]* 7.2 Write property test: CORS preflight response invariant
    - **Property 2: CORS preflight response invariant**
    - Generate random registered route paths, send OPTIONS, verify 204 + all CORS headers
    - **Validates: Requirements 3.1, 3.2, 3.3, 3.4, 3.5**
  - [x]* 7.3 Write property test: CORS headers on non-OPTIONS responses
    - **Property 3: CORS headers on non-OPTIONS responses**
    - Generate random non-OPTIONS methods and paths, verify CORS headers present
    - **Validates: Requirements 3.6**
  - [x]* 7.4 Write property test: Error response format consistency
    - **Property 4: Error response format consistency**
    - Generate random non-existent paths, verify error response JSON structure
    - **Validates: Requirements 5.4, 8.1, 8.2, 8.3**
  - [x]* 7.5 Write property test: Invalid JWT tokens are rejected
    - **Property 5: Invalid JWT tokens are rejected**
    - Generate random strings as Bearer tokens, verify all get 401 on protected routes
    - **Validates: Requirements 4.2**
  - [x]* 7.6 Write property test: Non-existent paths return 404
    - **Property 6: Non-existent paths return 404**
    - Generate random path segments, verify 404 + RESOURCE_NOT_FOUND
    - **Validates: Requirements 5.1**

- [x] 8. Final checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- This is a TEST-ONLY spec — no production code changes
- All test files go in `backend/app/src/test/java/org/moqui/rest/e2e/`
- Uses `java.net.http.HttpClient` (Java standard library) — no new dependencies
- Uses `JwtUtil.createAccessToken()` for generating real JWT tokens in auth tests
- Each test class manages its own server lifecycle via `E2eTestBase`
- Server uses null ECF — tests don't touch Moqui entity layer
- Property tests use jqwik 1.9.0 (already in pom.xml)
