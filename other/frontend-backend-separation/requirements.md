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

# Requirements Document

## Introduction

Phase 3 of the Moqui ERP modernization project. The goal is to migrate the Vue 3 + Electron frontend from the legacy `/rest/s1/novel-anime` API endpoints to the new standardized `/api/v1/` REST API layer built in Phase 2 (07-02). This achieves clean frontend-backend separation with typed API contracts, standardized error handling, and proper JWT token lifecycle management.

## Glossary

- **API_Client**: The unified TypeScript HTTP client module that handles all communication with the `/api/v1/` backend endpoints, including request/response interceptors, token injection, and error normalization
- **Auth_Service**: The frontend authentication service module responsible for login, logout, token refresh, and token lifecycle management against `/api/v1/auth/*` endpoints
- **Auth_Store**: The Pinia store that manages authentication state (user, tokens, isAuthenticated) and coordinates with Auth_Service
- **Entity_Service**: The frontend service module that provides typed CRUD operations via the generic `/api/v1/entities/{entityName}` endpoint
- **Service_Invoker**: The frontend module that invokes backend Moqui services via `/api/v1/services/{serviceName}`
- **Error_Handler**: The frontend module that normalizes backend error responses (error codes, field-level validation errors) into a consistent format for UI consumption
- **Legacy_API**: The old REST API at `/rest/s1/novel-anime` that the frontend currently uses
- **New_API**: The new REST API at `/api/v1/` built in Phase 2 with standardized JSON responses
- **Standardized_Response**: The backend response format `{ success: boolean, data: {...}, meta: {...}, error: { code, message, details } }`
- **Domain_Service**: A frontend service module (e.g., novelApi, characterApi) that provides domain-specific operations by composing Entity_Service and Service_Invoker calls

## Requirements

### Requirement 1: Unified API Client

**User Story:** As a frontend developer, I want a single, well-typed API client that targets the new `/api/v1/` endpoints, so that all HTTP communication goes through one consistent layer with proper authentication, error handling, and response parsing.

#### Acceptance Criteria

1. THE API_Client SHALL create an axios instance with base URL configurable via `VITE_API_V1_BASE_URL` environment variable, defaulting to `http://localhost:8080/api/v1`
2. WHEN sending any request, THE API_Client SHALL attach the JWT access token from localStorage as a `Bearer` token in the `Authorization` header
3. WHEN a response is received, THE API_Client SHALL parse the Standardized_Response format and return typed `{ success, data, meta, error }` objects to callers
4. WHEN a 401 response is received, THE API_Client SHALL attempt to refresh the access token using the refresh token before retrying the original request
5. IF the token refresh also fails, THEN THE API_Client SHALL clear all stored tokens and emit an authentication-expired event
6. WHEN a 429 response is received, THE API_Client SHALL extract the `Retry-After` header and include it in the error object
7. THE API_Client SHALL provide generic typed methods: `get<T>`, `post<T>`, `put<T>`, `delete<T>` that accept path, params/body, and return `Promise<ApiResult<T>>`

### Requirement 2: Authentication Migration

**User Story:** As a user, I want to log in, refresh my session, and log out using the new auth endpoints, so that my authentication works with the new API layer.

#### Acceptance Criteria

1. WHEN a user submits login credentials, THE Auth_Service SHALL send a POST request to `/api/v1/auth/login` with `{ username, password }` and store the returned `accessToken` and `refreshToken`
2. WHEN the access token is near expiration or expired, THE Auth_Service SHALL send a POST request to `/api/v1/auth/refresh` with the refresh token and update stored tokens
3. WHEN a user logs out, THE Auth_Service SHALL send a POST request to `/api/v1/auth/logout` and clear all locally stored tokens and user data
4. THE Auth_Store SHALL persist tokens to localStorage under existing keys (`novel_anime_access_token`, `novel_anime_refresh_token`) to maintain backward compatibility
5. WHEN the Auth_Service receives a login response, THE Auth_Store SHALL extract and store user information from the `data` field of the Standardized_Response
6. IF a login attempt fails, THEN THE Auth_Service SHALL return the error code and message from the Standardized_Response error object

### Requirement 3: Entity API Integration

**User Story:** As a frontend developer, I want to use the generic Entity CRUD API for data operations, so that I can perform create, read, update, delete operations on any entity through a single consistent interface.

#### Acceptance Criteria

1. THE Entity_Service SHALL provide typed methods for CRUD operations: `list(entityName, params)`, `get(entityName, id)`, `create(entityName, data)`, `update(entityName, id, data)`, `delete(entityName, id)`
2. WHEN listing entities, THE Entity_Service SHALL support query parameters for filtering, sorting (`orderBy`), pagination (`pageIndex`, `pageSize`), and field selection (`fields`)
3. WHEN a list response is received, THE Entity_Service SHALL extract pagination metadata from the `meta` field and return it alongside the data array
4. WHEN creating an entity, THE Entity_Service SHALL send a POST to `/api/v1/entities/{entityName}` and return the created entity data from the response
5. IF the backend returns a validation error with field-level details, THEN THE Entity_Service SHALL pass through the `error.details` array containing `{ field, message }` objects

### Requirement 4: Domain Service Migration

**User Story:** As a frontend developer, I want the existing domain-specific API services (novelApi, characterApi, sceneApi, episodeApi, creditsApi, pipelineApi) to use the new API layer internally, so that all data flows through the `/api/v1/` endpoints while maintaining the same public interface.

#### Acceptance Criteria

1. WHEN a Domain_Service method is called, THE Domain_Service SHALL route the request through either Entity_Service or Service_Invoker depending on the operation type
2. THE Domain_Service modules SHALL maintain their existing public method signatures to avoid breaking changes in consuming components
3. WHEN the backend service name differs from the legacy endpoint path, THE Domain_Service SHALL map the old operation to the correct `/api/v1/services/{serviceName}` or `/api/v1/entities/{entityName}` call
4. WHEN a Domain_Service receives a Standardized_Response, THE Domain_Service SHALL transform the response to match the existing return type expected by consuming components
5. IF a legacy endpoint has no equivalent in the new API, THEN THE Domain_Service SHALL document the gap and fall back to the legacy endpoint with a deprecation warning logged to console

### Requirement 5: Error Handling Alignment

**User Story:** As a frontend developer, I want error handling to be consistent with the new API's standardized error format, so that I can display meaningful error messages and handle field-level validation errors.

#### Acceptance Criteria

1. WHEN the API_Client receives an error response, THE Error_Handler SHALL map the backend error code (e.g., `VALIDATION_ERROR`, `AUTHENTICATION_REQUIRED`, `ACCESS_DENIED`, `RATE_LIMIT_EXCEEDED`) to the frontend `ErrorCode` enum
2. WHEN a validation error with field-level details is received, THE Error_Handler SHALL preserve the `details` array containing `{ field, message }` objects for form-level error display
3. THE Error_Handler SHALL provide a `getErrorMessage(code)` function that returns user-friendly Chinese error messages for each error code
4. IF a network error occurs (no response from server), THEN THE Error_Handler SHALL return an error with code `NETWORK_ERROR` and an appropriate message
5. WHEN an error with code `TOKEN_EXPIRED` is received, THE Error_Handler SHALL trigger the token refresh flow before surfacing the error to the caller

### Requirement 6: Environment Configuration

**User Story:** As a developer, I want environment-specific API configuration, so that the frontend can target different backend URLs in development and production.

#### Acceptance Criteria

1. THE API_Client SHALL read the API base URL from `VITE_API_V1_BASE_URL` environment variable
2. THE `.env.development` file SHALL contain `VITE_API_V1_BASE_URL=http://localhost:8080/api/v1` for local development
3. WHEN `VITE_API_V1_BASE_URL` is not set, THE API_Client SHALL default to `http://localhost:8080/api/v1`
4. THE `.env.development` file SHALL retain the legacy `VITE_API_BASE_URL` variable during the migration period for backward compatibility

### Requirement 7: TypeScript Type Definitions

**User Story:** As a frontend developer, I want TypeScript types that match the new API's standardized response format, so that I get compile-time type safety for all API interactions.

#### Acceptance Criteria

1. THE type definitions SHALL include a generic `ApiV1Response<T>` interface matching `{ success: boolean, data?: T, meta?: ResponseMeta, error?: ApiV1Error }`
2. THE type definitions SHALL include an `ApiV1Error` interface matching `{ code: string, message: string, details?: Array<{ field: string, message: string }> }`
3. THE type definitions SHALL include a `ResponseMeta` interface matching `{ totalCount?: number, pageIndex?: number, pageSize?: number, pageMaxIndex?: number }`
4. THE type definitions SHALL include typed interfaces for auth responses (`LoginResponse`, `RefreshResponse`) matching the new API contract
5. THE type definitions SHALL include error code constants matching the backend error codes: `VALIDATION_ERROR`, `AUTHENTICATION_REQUIRED`, `TOKEN_EXPIRED`, `ACCESS_DENIED`, `RESOURCE_NOT_FOUND`, `RATE_LIMIT_EXCEEDED`, `INTERNAL_ERROR`

### Requirement 8: Token Lifecycle Management

**User Story:** As a user, I want my session to stay active seamlessly through automatic token refresh, so that I don't get unexpectedly logged out during normal usage.

#### Acceptance Criteria

1. WHEN the API_Client receives a 401 response with error code `TOKEN_EXPIRED`, THE API_Client SHALL automatically attempt a token refresh and retry the failed request
2. THE API_Client SHALL queue concurrent requests that fail with 401 during a token refresh, and replay them after the refresh completes
3. IF the refresh token is also expired or invalid, THEN THE Auth_Store SHALL clear authentication state and redirect the user to the login screen
4. THE Auth_Store SHALL validate token expiration locally before making API calls, and proactively refresh tokens that expire within 5 minutes
5. WHEN tokens are refreshed, THE Auth_Store SHALL update both localStorage and in-memory state atomically

### Requirement 9: Backward Compatibility

**User Story:** As a developer, I want the migration to be incremental, so that the app can work during the transition period without breaking existing functionality.

#### Acceptance Criteria

1. THE API_Client SHALL support a configuration flag `USE_LEGACY_API` that, when true, routes requests through the old `/rest/s1/novel-anime` base URL
2. WHEN a Domain_Service encounters an endpoint not yet available in the new API, THE Domain_Service SHALL fall back to the legacy endpoint and log a deprecation warning
3. THE migration SHALL preserve all existing localStorage key names for tokens and user data to avoid breaking existing sessions
4. WHEN the new API client is initialized, THE API_Client SHALL attempt a health check to the new API and fall back to legacy mode if the new API is unreachable
