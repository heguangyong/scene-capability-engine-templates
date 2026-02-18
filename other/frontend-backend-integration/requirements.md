---
name: frontend-backend-integration
category: other
description: Template for Frontend Backend Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

本 Spec 聚焦前端（Vue 3 + Electron）与后端（Java 21 + Embedded Jetty REST API）的认证集成。后端已提供完整的 JWT 认证 + RBAC 授权 API（`/api/v1/auth/*`），前端已有基础的 HTTP 客户端（`apiV1Client`）、Token 管理（`tokenManager`）和认证服务（`authServiceV1`）。本 Spec 的目标是补齐前端缺失的集成环节：用户资料获取、Store 职责统一、路由守卫强化、错误码映射、开发模式认证跳过清理，以及属性测试覆盖。

本 Spec 仅修改前端代码，不涉及后端变更。

## Glossary

- **Auth_Store**: Pinia 认证状态管理 Store（`stores/auth.ts`），管理认证状态、Token、用户信息
- **User_Store**: Pinia 用户状态管理 Store（`stores/user.ts`），管理用户资料
- **ApiV1Client**: Axios 封装的 HTTP 客户端（`services/apiV1Client.ts`），自动附加 Bearer Token，处理 401 自动刷新
- **TokenManager**: Token 管理器（`services/tokenManager.ts`），负责 Token 存储、解码、过期检查、刷新
- **AuthServiceV1**: 认证服务（`services/authServiceV1.ts`），封装 `/api/v1/auth/*` 接口调用
- **Router_Guard**: Vue Router 导航守卫，拦截路由跳转并检查认证状态
- **Error_Code**: 后端返回的标准错误码（`VALIDATION_ERROR`、`AUTHENTICATION_REQUIRED`、`TOKEN_EXPIRED`、`ACCESS_DENIED`、`RATE_LIMIT_EXCEEDED`）
- **Skip_Auth_Flag**: 环境变量 `VITE_SKIP_AUTH`，当前在开发模式下跳过认证

## Requirements

### Requirement 1: User Profile Fetch

**User Story:** As a user, I want the system to fetch my real profile from the backend after login, so that I see accurate user information (userId, username, groups) instead of locally constructed data.

#### Acceptance Criteria

1. WHEN a user successfully logs in, THE AuthServiceV1 SHALL call `GET /api/v1/auth/me` to fetch the user profile containing userId, username, and groups
2. WHEN the `/auth/me` response is successful, THE Auth_Store SHALL update user state with the backend-provided userId, username, and groups
3. IF the `/auth/me` request fails, THEN THE Auth_Store SHALL retain the login-response data as fallback and log a warning
4. WHEN the application initializes with a valid stored token, THE Auth_Store SHALL call `GET /api/v1/auth/me` to restore the user profile

### Requirement 2: Auth State Synchronization

**User Story:** As a developer, I want a single source of truth for auth state, so that login/logout behavior is consistent across the application.

#### Acceptance Criteria

1. THE Auth_Store SHALL be the single source of truth for authentication state (tokens, isAuthenticated, user profile)
2. THE User_Store SHALL delegate all login and logout operations to Auth_Store instead of calling authApi directly
3. WHEN Auth_Store completes a login, THE User_Store SHALL synchronize its user profile fields from Auth_Store
4. WHEN Auth_Store completes a logout, THE User_Store SHALL clear all user profile fields
5. WHEN the `auth-expired` CustomEvent is dispatched, THE Auth_Store SHALL clear authentication state and THE Router_Guard SHALL redirect to the login page

### Requirement 3: Token Refresh Integration

**User Story:** As a user, I want my session to stay alive transparently, so that I don't get logged out unexpectedly while using the application.

#### Acceptance Criteria

1. WHEN ApiV1Client receives a 401 response with `TOKEN_EXPIRED` error code, THE ApiV1Client SHALL attempt to refresh the token via TokenManager and retry the original request exactly once
2. WHILE a token refresh is in progress, THE TokenManager SHALL queue concurrent requests and resolve them all with the new token once refresh completes
3. IF the token refresh fails, THEN THE TokenManager SHALL clear all tokens, emit the `auth-expired` event, and reject all queued requests
4. THE TokenManager SHALL decode JWT payloads correctly for both standard and URL-safe Base64 encoded tokens

### Requirement 4: Error Code Handling

**User Story:** As a user, I want to see clear, localized error messages when something goes wrong, so that I understand what happened and what to do next.

#### Acceptance Criteria

1. THE ApiV1Client SHALL map backend Error_Code values to user-friendly Chinese error messages
2. WHEN the Error_Code is `TOKEN_EXPIRED`, THE error message SHALL indicate the session has expired and prompt re-login
3. WHEN the Error_Code is `RATE_LIMIT_EXCEEDED`, THE error message SHALL indicate rate limiting and display the retryAfter value if available
4. WHEN the Error_Code is `ACCESS_DENIED`, THE error message SHALL indicate insufficient permissions
5. WHEN the Error_Code is `AUTHENTICATION_REQUIRED`, THE error message SHALL indicate login is required
6. WHEN the Error_Code is `VALIDATION_ERROR`, THE error message SHALL include field-level error details when available
7. IF an unknown Error_Code is received, THEN THE ApiV1Client SHALL return a generic error message with the original error code

### Requirement 5: Route Protection

**User Story:** As a user, I want protected pages to require authentication, so that unauthenticated access is prevented.

#### Acceptance Criteria

1. WHEN a user navigates to a route with `requiresAuth: true` and has no valid token, THE Router_Guard SHALL redirect to `/login` with the original path as a `redirect` query parameter
2. WHEN a user navigates to a route with `requiresAuth: true` and has a valid token, THE Router_Guard SHALL allow navigation
3. WHEN a user navigates to the login page while already authenticated, THE Router_Guard SHALL redirect to `/dashboard`
4. THE Router_Guard SHALL validate token existence and expiry via TokenManager instead of only checking localStorage directly

### Requirement 6: Skip Auth Flag Cleanup

**User Story:** As a developer, I want the `VITE_SKIP_AUTH` flag to only work in test mode, so that development mode always exercises the real auth flow.

#### Acceptance Criteria

1. THE application SHALL only honor `VITE_SKIP_AUTH=true` when `NODE_ENV` is `test`
2. WHEN `VITE_SKIP_AUTH=true` and `NODE_ENV` is not `test`, THE Router_Guard SHALL enforce normal authentication checks
3. WHEN `VITE_SKIP_AUTH` is removed or set to `false`, THE application SHALL function identically to when the flag does not exist

### Requirement 7: Property-Based Tests

**User Story:** As a developer, I want property-based tests for core auth logic, so that edge cases in token handling, error normalization, and auth state transitions are caught automatically.

#### Acceptance Criteria

1. THE test suite SHALL include fast-check property tests for TokenManager JWT payload decoding that verify round-trip correctness for valid payloads
2. THE test suite SHALL include fast-check property tests for TokenManager expiry checking that verify correct behavior across a range of expiration timestamps
3. THE test suite SHALL include fast-check property tests for error code mapping that verify every known Error_Code maps to a non-empty Chinese message
4. THE test suite SHALL include fast-check property tests for error normalization that verify ApiV1Client always produces a valid `ApiV1Result` structure regardless of input error shape
5. THE test suite SHALL run each property test for a minimum of 100 iterations
