---
name: rest-api-layer
category: other
description: Template for Rest Api Layer
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

﻿# Implementation Plan: REST API Layer

## Overview

Implement the REST API layer as a Moqui component (`rest-api`) that enhances the existing REST API infrastructure. The implementation uses Java for new code (aligning with the Groovy-to-Java migration direction), Moqui XML service definitions for API endpoints, and jqwik for property-based testing.

## Tasks

- [x] 1 Set up rest-api component structure and dependencies
  - [x] 1.1 Create Moqui component directory structure and component.xml
    - Create `backend/runtime/component/rest-api/` with subdirectories: `entity/`, `service/`, `service/api/`, `src/main/java/org/moqui/api/`, `src/test/java/org/moqui/api/`, `src/test/groovy/`, `data/`
    - Create `component.xml` with component name "rest-api" and proper dependency declarations
    - Create `build.gradle` with jqwik test dependency (`net.jqwik:jqwik:1.9.0`)
    - _Requirements: 1.1, 1.5, 10.1_

  - [x] 1.2 Create ApiResponse utility class
    - Implement `src/main/java/org/moqui/api/ApiResponse.java`
    - Static methods: `success(data)`, `success(data, meta)`, `error(httpStatus, code, message)`, `error(httpStatus, code, message, details)`, `validationError(fieldErrors)`
    - Standardized JSON response structure with `success`, `data`, `error`, `meta` fields
    - _Requirements: 1.4, 9.1_

  - [x]* 1.3 Write property test for ApiResponse format consistency
    - **Property 1: JSON Response Format Consistency**
    - Generate random success/error data, verify all responses have correct structure
    - **Validates: Requirements 1.2, 1.4, 9.1**

  - [x] 1.4 Create InputSanitizer utility class
    - Implement `src/main/java/org/moqui/api/InputSanitizer.java`
    - Methods: `sanitize(String)`, `sanitizeEntityName(String)`, `sanitizeServiceName(String)`, `validatePayloadSize(request, maxBytes)`
    - Prevent SQL injection, XSS, path traversal in entity/service names
    - _Requirements: 11.1, 9.9_

  - [x]* 1.5 Write property test for InputSanitizer
    - **Property 19: Input Sanitization**
    - Generate strings with injection patterns (SQL, XSS, path traversal), verify sanitized output
    - **Validates: Requirements 11.1**

- [x] 2 Checkpoint - Verify component structure and utilities compile
  - Ensure all tests pass, ask the user if questions arise.

- [x] 3 Implement authentication and authorization enhancements
  - [x] 3.1 Create AuthApiServices.xml with login, refresh, and logout services
    - Implement `service/api/AuthApiServices.xml` with Moqui service definitions
    - `login#User`: Validate credentials via UserFacade, return JwtUtil.TokenPair, track failed attempts
    - `refresh#Token`: Validate refresh token via JwtUtil.refreshAccessToken(), return new TokenPair
    - `logout#User`: Revoke token via JwtUtil.revokeToken()
    - Implement account lockout after 5 failed attempts using UserAccount.successiveFailedLogins
    - _Requirements: 2.1, 2.2, 2.5, 2.6, 11.5_

  - [x]* 3.2 Write property test for JWT token properties
    - **Property 4: JWT Token Properties**
    - Generate random userIds, create tokens, verify claims (userId, expiration, algorithm)
    - **Validates: Requirements 2.1, 2.3, 2.4**

  - [x]* 3.3 Write property test for token refresh round-trip
    - **Property 5: Token Refresh Round-Trip**
    - Generate tokens, refresh them, verify userId is preserved
    - **Validates: Requirements 2.6**

  - [x]* 3.4 Write property test for account lockout
    - **Property 20: Account Lockout**
    - Simulate N failed login attempts, verify lockout triggers at threshold 5
    - **Validates: Requirements 11.5**

  - [x] 3.5 Create RateLimiter class
    - Implement `src/main/java/org/moqui/api/RateLimiter.java`
    - Sliding window counter per userId/IP using ConcurrentHashMap
    - Configurable max requests per minute (default 60)
    - Methods: `isAllowed(key)`, `recordRequest(key)`
    - _Requirements: 1.8_

  - [x]* 3.6 Write property test for rate limiter
    - **Property 3: Rate Limiter Enforcement**
    - Generate random request sequences, verify enforcement at configured limit
    - **Validates: Requirements 1.8**

- [x] 4 Checkpoint - Verify auth and rate limiting
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5 Implement Entity CRUD API
  - [x] 5.1 Create entity data model for AsyncJobRecord
    - Create `entity/ApiEntities.xml` with AsyncJobRecord entity definition
    - Fields: jobId, serviceName, parameters, status, result, errorMessage, userId, createdDate, completedDate
    - _Requirements: 5.6, 5.7, 5.8_

  - [x] 5.2 Create EntityApiServices.xml with CRUD operations
    - Implement `service/api/EntityApiServices.xml` with Moqui service definitions
    - `list#Entity`: Use EntityFacade.find() with searchFormMap(), support filtering, sorting, field selection, pagination
    - `get#Entity`: Find by primary key with field selection support
    - `create#Entity`: Create via ServiceFacade (uses implicit CRUD services), support batch via JSON array
    - `update#Entity`: Update via ServiceFacade implicit CRUD
    - `delete#Entity`: Delete via ServiceFacade implicit CRUD
    - Parse `fields` query param for selectField(), `orderBy` for orderBy(), filter params for condition()
    - Return standardized ApiResponse format with pagination metadata
    - _Requirements: 4.1-4.12_

  - [x]* 5.3 Write property test for entity CRUD round-trip
    - **Property 8: Entity CRUD Round-Trip**
    - Generate random entity data, test create→get→update→get→delete→get(404) cycle
    - **Validates: Requirements 4.2, 4.3, 4.4, 4.5**

  - [x]* 5.4 Write property test for entity pagination consistency
    - **Property 12: Entity Pagination Consistency**
    - Generate random totalCount and pageSize, verify pagination math
    - **Validates: Requirements 4.1, 4.9**

  - [x]* 5.5 Write property test for entity filtering
    - **Property 9: Entity List Filtering**
    - Generate entity lists with filter criteria, verify all results match filter
    - **Validates: Requirements 4.6**

- [x] 6 Checkpoint - Verify Entity API
  - Ensure all tests pass, ask the user if questions arise.

- [x] 7 Implement Service API
  - [x] 7.1 Create ServiceApiServices.xml with sync and async invocation
    - Implement `service/api/ServiceApiServices.xml`
    - `invoke#Service`: Validate service exists, validate required params, call sync or async based on X-Async header
    - `get#JobStatus`: Query AsyncJobRecord by jobId, return status and result
    - For async: Create AsyncJobRecord with PENDING status, use ServiceFacade.async(), update record on completion
    - _Requirements: 5.1, 5.4, 5.6-5.10_

  - [x]* 7.2 Write property test for service parameter validation
    - **Property 16: Service Parameter Validation**
    - Generate service calls with missing required params, verify 400 response with details
    - **Validates: Requirements 5.9**

- [x] 8 Implement Screen Definition API
  - [x] 8.1 Create ScreenJsonConverter utility class
    - Implement `src/main/java/org/moqui/api/ScreenJsonConverter.java`
    - Convert MNode (XML screen definition) to Map (JSON-serializable)
    - Extract: form definitions, field metadata (type, validation, default), transitions, referenced services/entities
    - Handle nested screen sections and sub-screens
    - _Requirements: 6.1-6.4, 6.8_

  - [x] 8.2 Create ScreenApiServices.xml
    - Implement `service/api/ScreenApiServices.xml`
    - `get#ScreenDefinition`: Load screen via ScreenFacade, convert via ScreenJsonConverter, support parameter passing
    - Enforce screen artifact authorization
    - _Requirements: 6.1, 6.5, 6.6_

  - [x]* 8.3 Write property test for screen definition completeness
    - **Property 18: Screen Definition Completeness**
    - For sample screens, verify JSON output contains forms, transitions, and metadata
    - **Validates: Requirements 6.1, 6.2, 6.3, 6.4, 6.8**

- [x] 9 Checkpoint - Verify Service and Screen APIs
  - Ensure all tests pass, ask the user if questions arise.

- [x] 10 Implement OpenAPI 3.0 and documentation
  - [x] 10.1 Create OpenApiGenerator class
    - Implement `src/main/java/org/moqui/api/OpenApiGenerator.java`
    - Convert existing Swagger 2.0 output from RestApi.getSwaggerMap() to OpenAPI 3.0 format
    - Add security schemes (Bearer JWT), component schemas, server info
    - Generate schemas from EntityDefinition and ServiceDefinition
    - _Requirements: 7.1, 7.2, 7.4_

  - [x] 10.2 Create DocsApiServices.xml
    - Implement `service/api/DocsApiServices.xml`
    - `get#OpenApiSpec`: Generate and return OpenAPI 3.0 JSON
    - Serve Swagger UI static files at /api/docs (bundle swagger-ui-dist)
    - _Requirements: 7.1, 7.3_

  - [x]* 10.3 Write property test for OpenAPI spec validity
    - **Property 22: OpenAPI Spec Validity**
    - Generate spec, verify it's valid OpenAPI 3.0 with all registered endpoints
    - **Validates: Requirements 7.1, 7.2**

- [x] 11 Implement REST API route definitions and wiring
  - [x] 11.1 Create api.rest.xml route definitions
    - Implement `service/api.rest.xml` following rest-api-3.xsd schema
    - Define all routes: /api/v1/entities, /api/v1/services, /api/v1/screens, /api/v1/auth, /api/v1/docs
    - Wire routes to corresponding service definitions
    - Set require-authentication appropriately (false for auth/login, docs; true for everything else)
    - _Requirements: 1.1, 1.5_

  - [x] 11.2 Add security headers and error handling middleware
    - Enhance response handling to add security headers (X-Content-Type-Options, X-Frame-Options, X-XSS-Protection)
    - Implement centralized error handler mapping Moqui exceptions to standardized error responses
    - Implement error message sanitization for production mode
    - _Requirements: 9.1-9.9, 11.3_

  - [x]* 11.3 Write property test for error message sanitization
    - **Property 21: Error Message Sanitization**
    - Generate error responses, verify no stack traces or internal details leak
    - **Validates: Requirements 9.9**

  - [x]* 11.4 Write integration tests for full API flow
    - Test end-to-end: login → get token → call entity API → call service API → logout
    - Test error scenarios: invalid token, expired token, missing permissions
    - _Requirements: 10.1, 10.5_

- [x] 12 Final checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- The implementation is structured as a Moqui component, keeping all code isolated from the framework core
- Property tests use jqwik with minimum 100 iterations per property
- Integration tests use Moqui's existing Spock test infrastructure
- All new Java code goes in `src/main/java/org/moqui/api/` within the component

