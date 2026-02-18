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

# Implementation Plan: Frontend-Backend Integration

## Overview

将前端认证层与后端 REST API 完成端到端集成。按模块递增实现：错误映射 → 类型定义 → AuthServiceV1 扩展 → Auth_Store 重构 → User_Store 同步 → Router Guard 强化 → Skip Auth 清理 → 属性测试。

## Tasks

- [x] 1. Create error mapper module and types
  - [x] 1.1 Add `UserProfile` type to `types/apiV1.ts` with `userId`, `username`, `groups` fields
    - _Requirements: 1.1, 1.2_
  - [x] 1.2 Create `services/errorMapper.ts` with error code to Chinese message mapping
    - Implement `mapErrorToMessage(error: ApiV1Error): string`
    - Implement `formatRateLimitMessage(error: ApiV1Error): string` that includes retryAfter
    - Implement `formatValidationErrors(error: ApiV1Error): string` that includes field names
    - Handle unknown error codes by preserving original code in message
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7_
  - [x]* 1.3 Write property tests for error mapper (`services/__tests__/errorMapper.test.ts`)
    - **Property 3: Known Error Code Mapping Completeness**
    - **Validates: Requirements 4.1, 4.2, 4.4, 4.5**
    - **Property 4: Rate Limit Message Includes RetryAfter**
    - **Validates: Requirements 4.3**
    - **Property 5: Validation Error Includes Field Details**
    - **Validates: Requirements 4.6**
    - **Property 6: Unknown Error Code Preservation**
    - **Validates: Requirements 4.7**

- [x] 2. Extend AuthServiceV1 with fetchProfile
  - [x] 2.1 Add `fetchProfile()` method to `services/authServiceV1.ts` that calls `GET /api/v1/auth/me`
    - Return `ApiV1Result<UserProfile>`
    - _Requirements: 1.1_

- [x] 3. Refactor Auth_Store as single source of truth
  - [x] 3.1 Refactor `stores/auth.ts` to use `AuthUser` type with `groups` field
    - Update `login()` to call `authServiceV1.fetchProfile()` after successful login
    - Fallback to login response data if `/auth/me` fails
    - Update `initialize()` to call `fetchProfile()` when restoring from stored token
    - Use `errorMapper` for user-facing error messages
    - Register `auth-expired` event listener that clears state and redirects to `/login`
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 2.1, 2.5, 4.1_

- [x] 4. Refactor User_Store to sync from Auth_Store
  - [x] 4.1 Refactor `stores/user.ts` to remove direct login/logout API calls
    - Add `syncFromAuthStore(authUser)` method
    - Remove `login()` and `logout()` methods that call authApi directly
    - Keep profile-related methods (updateProfile, changePassword, uploadAvatar) as-is
    - _Requirements: 2.2, 2.3, 2.4_

- [x] 5. Update LoginView to use unified auth flow
  - [x] 5.1 Update `views/LoginView.vue` to use Auth_Store for login and sync User_Store
    - Call `authStore.login()` then `userStore.syncFromAuthStore()`
    - Remove direct `userStore.login()` call
    - _Requirements: 2.1, 2.3_

- [x] 6. Checkpoint - Ensure core auth flow works
  - Ensure all tests pass, ask the user if questions arise.

- [x] 7. Strengthen Router Guard and Skip Auth cleanup
  - [x] 7.1 Update `router/index.js` to validate token via TokenManager
    - Replace `checkAuthStatus()` to use `tokenManager.getAccessToken()` + `tokenManager.isTokenExpiringSoon()`
    - Add `shouldSkipAuth()` that only returns true when `VITE_SKIP_AUTH=true` AND `NODE_ENV=test`
    - Ensure redirect to `/login` with `redirect` query param for protected routes
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 6.1, 6.2, 6.3_

- [x] 8. Extend TokenManager property tests
  - [x]* 8.1 Add JWT decode round-trip property test to `services/__tests__/tokenManager.test.ts`
    - **Property 1: JWT Decode Round-Trip**
    - **Validates: Requirements 3.4**
  - [x]* 8.2 Add concurrent refresh deduplication property test to `services/__tests__/tokenManager.test.ts`
    - **Property 2: Concurrent Refresh Deduplication**
    - **Validates: Requirements 3.2**

- [x] 9. Create ApiV1Client error normalization property test
  - [x]* 9.1 Create `services/__tests__/apiV1Client.test.ts` with error normalization test
    - **Property 7: Error Normalization Structural Validity**
    - **Validates: Requirements 7.4**

- [x] 10. Final checkpoint - Ensure all tests pass
  - Run `npm test` in `frontend/` directory
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- All changes are frontend-only — no backend modifications
- Use existing `apiV1Client` for all API calls
- Use existing `tokenManager` for token operations
- Tests use vitest + jsdom + fast-check with `{ numRuns: 100 }`
- Property tests reference design document properties by number
