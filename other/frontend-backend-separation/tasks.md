---
name: frontend-backend-separation
category: other
description: Template for Frontend Backend Separation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: Frontend-Backend Separation

## Overview

Migrate the Vue 3 + Electron frontend from legacy `/rest/s1/novel-anime` to the new `/api/v1/` REST API. Implementation follows a bottom-up approach: types → HTTP client → token manager → entity/service abstractions → domain service migration → store updates → environment config.

## Tasks

- [x] 1 Create TypeScript type definitions and update environment config
  - [x] 1.1 Create `frontend/src/renderer/types/apiV1.ts` with `ApiV1Response<T>`, `ApiV1Error`, `ResponseMeta`, `FieldError`, `ApiV1Result<T>`, `LoginResponse`, `RefreshResponse`, `JwtPayload`, and `ApiErrorCodes` constant
    - Match the backend standardized response format exactly
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5_
  - [x] 1.2 Update `frontend/src/renderer/types/api.ts` to add `RATE_LIMITED` and `TOKEN_EXPIRED` to the `ErrorCode` enum
    - _Requirements: 5.1, 7.5_
  - [x] 1.3 Update `frontend/.env.development` to add `VITE_API_V1_BASE_URL=http://localhost:8080/api/v1` while preserving existing `VITE_API_BASE_URL`
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

- [x] 2 Implement core HTTP client layer
  - [x] 2.1 Create `frontend/src/renderer/services/tokenManager.ts`
    - Implement `getAccessToken()`, `getRefreshToken()`, `setTokens()`, `clearTokens()`
    - Implement `decodeTokenPayload()` for local JWT decoding
    - Implement `isTokenExpiringSoon()` checking 5-minute window
    - Implement `refreshAccessToken()` with concurrent request queuing
    - Use existing localStorage keys: `novel_anime_access_token`, `novel_anime_refresh_token`
    - _Requirements: 2.1, 2.4, 8.1, 8.2, 8.4, 8.5, 9.3_
  - [x]* 2.2 Write property tests for TokenManager
    - **Property 3: Token Persistence Round-Trip**
    - **Validates: Requirements 2.1, 2.4, 8.5, 9.3**
    - **Property 12: Local JWT Expiration Validation**
    - **Validates: Requirements 8.4**
  - [x] 2.3 Create `frontend/src/renderer/services/apiV1Client.ts`
    - Create axios instance with `VITE_API_V1_BASE_URL` base URL
    - Request interceptor: attach Bearer token via TokenManager
    - Response interceptor: parse `ApiV1Response` format into `ApiV1Result`
    - 401 handling: delegate to TokenManager for refresh + retry
    - 429 handling: extract `Retry-After` header
    - Implement typed `get<T>`, `post<T>`, `put<T>`, `delete<T>` methods
    - Support `USE_LEGACY_API` flag for fallback routing
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 9.1_
  - [x]* 2.4 Write property tests for ApiV1Client
    - **Property 1: Bearer Token Attachment**
    - **Validates: Requirements 1.2**
    - **Property 2: Standardized Response Parsing**
    - **Validates: Requirements 1.3, 3.3**
    - **Property 11: Concurrent 401 Request Queuing**
    - **Validates: Requirements 8.2**

- [x] 3 Implement API abstraction layer
  - [x] 3.1 Create `frontend/src/renderer/services/entityService.ts`
    - Implement `list<T>(entityName, params)` with filtering, sorting, pagination, field selection
    - Implement `get<T>(entityName, id, fields?)`, `create<T>`, `update<T>`, `delete`
    - Construct URLs as `/entities/{entityName}` and `/entities/{entityName}/{id}`
    - Parse `meta` from response for pagination info
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
  - [x]* 3.2 Write property tests for EntityService
    - **Property 6: Entity Service URL Construction**
    - **Validates: Requirements 3.2, 3.4**
    - **Property 7: Validation Error Detail Preservation**
    - **Validates: Requirements 3.5, 5.2**
  - [x] 3.3 Create `frontend/src/renderer/services/serviceInvoker.ts`
    - Implement `invoke<T>(serviceName, params)` via POST to `/services/{serviceName}`
    - Implement `invokeAsync(serviceName, params)` with `X-Async: true` header
    - Implement `getJobStatus(jobId)` via GET to `/services/jobs/{jobId}`
    - _Requirements: 4.1, 4.3_
  - [x] 3.4 Create `frontend/src/renderer/services/authServiceV1.ts`
    - Implement `login(username, password)` via POST to `/auth/login`
    - Implement `refresh()` via POST to `/auth/refresh`
    - Implement `logout()` via POST to `/auth/logout`, clear tokens
    - Parse responses using `ApiV1Response<LoginResponse>` and `ApiV1Response<RefreshResponse>`
    - _Requirements: 2.1, 2.2, 2.3, 2.5, 2.6_
  - [x]* 3.5 Write property tests for AuthServiceV1
    - **Property 4: Login Response User Extraction**
    - **Validates: Requirements 2.5**
    - **Property 5: Login Error Propagation**
    - **Validates: Requirements 2.6**

- [x] 4 Checkpoint - Core layer tests
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5 Update error handling
  - [x] 5.1 Update `frontend/src/renderer/services/errorHandler.ts`
    - Add `fromApiV1Error(error: ApiV1Error)` function to convert backend errors to `AppError`
    - Add backend error code → frontend `ErrorCode` mapping (BACKEND_ERROR_MAP)
    - Update `getErrorMessage()` with Chinese messages for new error codes (`RATE_LIMITED`, `TOKEN_EXPIRED`)
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_
  - [x]* 5.2 Write property tests for error handling
    - **Property 8: Error Code Mapping Completeness**
    - **Validates: Requirements 5.1, 5.3**

- [x] 6 Migrate domain services
  - [x] 6.1 Refactor `frontend/src/renderer/services/authApi.ts` to use AuthServiceV1 internally
    - Replace hardcoded `/rest/s1/novel-anime/auth/*` paths with AuthServiceV1 calls
    - Keep OAuth methods (GitHub, Google, WeChat) on legacy endpoints with deprecation warnings
    - Keep profile/password/avatar methods on legacy endpoints with deprecation warnings
    - Preserve all existing public method signatures
    - _Requirements: 2.1, 2.2, 2.3, 4.2, 4.5, 9.2_
  - [x] 6.2 Refactor `frontend/src/renderer/services/novelApi.ts` to use EntityService and ServiceInvoker
    - Map `listNovels` → `entityService.list('novel-anime.Novel', { filters: { projectId } })`
    - Map `getNovel` → `entityService.get('novel-anime.Novel', novelId)`
    - Map `importText` → `serviceInvoker.invoke('novel-anime.NovelServices.import#Text', data)`
    - Map `analyzeStructure` → `serviceInvoker.invoke('novel-anime.NovelServices.analyze#Structure', { novelId })`
    - Transform responses to match existing return types
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x] 6.3 Refactor `frontend/src/renderer/services/characterApi.ts` to use EntityService and ServiceInvoker
    - Map `getCharacters` → `entityService.list('novel-anime.Character', { filters })`
    - Map `getCharacter` → `entityService.get('novel-anime.Character', characterId)`
    - Map `updateCharacter` → `entityService.update('novel-anime.Character', characterId, updates)`
    - Map `deleteCharacter` → `entityService.delete('novel-anime.Character', characterId)`
    - Map `extractCharacters` → `serviceInvoker.invoke('novel-anime.CharacterServices.extract#Characters', { novelId })`
    - Map `mergeCharacters` → `serviceInvoker.invoke('novel-anime.CharacterServices.merge#Characters', data)`
    - Map `lockCharacter` → `serviceInvoker.invoke('novel-anime.CharacterServices.lock#Character', data)`
    - Map `createRelationship` → `serviceInvoker.invoke('novel-anime.CharacterServices.create#Relationship', data)`
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x] 6.4 Refactor `frontend/src/renderer/services/sceneApi.ts` to use EntityService and ServiceInvoker
    - Map `getScenes` → `entityService.list('novel-anime.Scene', { filters })`
    - Map `updateScene` → `entityService.update('novel-anime.Scene', sceneId, updates)`
    - Map `enhanceScenes` → `serviceInvoker.invoke('novel-anime.SceneServices.enhance#Scenes', { novelId })`
    - Map `approveScene` → `serviceInvoker.invoke('novel-anime.SceneServices.approve#Scene', data)`
    - Map `reanalyzeScene` → `serviceInvoker.invoke('novel-anime.SceneServices.reanalyze#Scene', data)`
    - Map `getSceneStats` → `serviceInvoker.invoke('novel-anime.SceneServices.get#SceneStats', { novelId })`
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x] 6.5 Refactor `frontend/src/renderer/services/episodeApi.ts` to use EntityService and ServiceInvoker
    - Map `getEpisodes` → `entityService.list('novel-anime.Episode', { filters })`
    - Map `updateEpisode` → `entityService.update('novel-anime.Episode', episodeId, updates)`
    - Map `deleteEpisode` → `entityService.delete('novel-anime.Episode', episodeId)`
    - Map `generateEpisodes` → `serviceInvoker.invoke('novel-anime.EpisodeServices.generate#Episodes', data)`
    - Map `generateScreenplay` → `serviceInvoker.invoke('novel-anime.EpisodeServices.generate#Screenplay', data)`
    - Map `adjustBoundaries` → `serviceInvoker.invoke('novel-anime.EpisodeServices.adjust#Boundaries', data)`
    - Map `getEpisodeStats` → `serviceInvoker.invoke('novel-anime.EpisodeServices.get#EpisodeStats', { novelId })`
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x] 6.6 Refactor `frontend/src/renderer/services/creditsApi.ts` to use ServiceInvoker and EntityService
    - Map `getBalance` → `serviceInvoker.invoke('novel-anime.CreditsServices.get#Balance')`
    - Map `consume` → `serviceInvoker.invoke('novel-anime.CreditsServices.consume#Credits', data)`
    - Map `getHistory` → `entityService.list('novel-anime.CreditHistory', { filters })`
    - Replace separate axios instance with shared ApiV1Client
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x] 6.7 Refactor `frontend/src/renderer/services/pipelineApi.ts` to use ServiceInvoker
    - Map `createPipeline` → `serviceInvoker.invoke('novel-anime.PipelineServices.create#Pipeline', { novelId })`
    - Map `getPipelineStatus` → `serviceInvoker.invoke('novel-anime.PipelineServices.get#PipelineStatus', { novelId })`
    - Map `updateProgress` → `serviceInvoker.invoke('novel-anime.PipelineServices.update#Progress', data)`
    - Map `completePipeline` → `serviceInvoker.invoke('novel-anime.PipelineServices.complete#Pipeline', data)`
    - Map `failPipeline` → `serviceInvoker.invoke('novel-anime.PipelineServices.fail#Pipeline', data)`
    - Keep `monitorPipeline` polling logic unchanged, just update the underlying call
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [x]* 6.8 Write property tests for domain service migration
    - **Property 9: Domain Service Response Transformation**
    - **Validates: Requirements 4.4**
    - **Property 10: Domain Service Endpoint Mapping**
    - **Validates: Requirements 4.3**

- [x] 7 Checkpoint - Domain service migration tests
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8 Update stores and main API service
  - [x] 8.1 Refactor `frontend/src/renderer/stores/auth.ts` to use AuthServiceV1
    - Replace `authApi` import with `authServiceV1` for login, logout, refresh, validateToken
    - Update `login()` to parse `ApiV1Response<LoginResponse>` format
    - Update `refreshAccessToken()` to use TokenManager
    - Keep OAuth actions (GitHub, Google, WeChat) using legacy authApi
    - Update `persistTokens()` to delegate to TokenManager
    - _Requirements: 2.1, 2.2, 2.3, 2.5, 8.1, 8.4, 8.5_
  - [x] 8.2 Refactor `frontend/src/renderer/services/api.ts` (main ApiService)
    - Update `login()`, `register()`, `logout()` to use AuthServiceV1
    - Update entity methods (`getProjects`, `createProject`, `getNovel`, etc.) to use EntityService
    - Keep the class as a facade that delegates to new services
    - Preserve `axiosInstance` property for any remaining direct consumers
    - _Requirements: 4.1, 4.2, 4.4, 9.1_
  - [x] 8.3 Update `frontend/src/renderer/services/index.ts` to export new services
    - Add exports for `apiV1Client`, `entityService`, `serviceInvoker`, `authServiceV1`, `tokenManager`
    - Keep existing exports unchanged
    - _Requirements: 4.2_

- [x] 9 Final checkpoint - Full integration

- [x] 10 Frontend integration for API discoverability endpoints
  - [x] 10.1 Add `apiDiscoveryService` for `/api/v1`, `/api/v1/catalog`, `/api/v1/routes`
    - Implement `getIndex()`, `getCatalog()`, `listRoutes({ tag, publicOnly })`
  - [x] 10.2 Add service tests for discovery endpoint path/param mapping
    - Validate default and filtered route query behavior
  - [x] 10.3 Export discovery service from `services/index.ts`
    - Keep existing exports and default facade compatibility
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- The legacy `api.ts` ApiService is preserved as a facade during migration
- OAuth endpoints (GitHub, Google, WeChat) remain on legacy API — no new API equivalent exists yet
- Entity names in the mapping table (e.g., `novel-anime.Novel`) may need adjustment based on actual Moqui entity definitions
- Property tests use fast-check (already in project dependencies)
