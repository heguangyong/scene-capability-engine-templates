---
name: pure-restful-backend
category: other
description: Template for Pure Restful Backend
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

﻿# Implementation Plan: Pure RESTful Backend

## Overview

Bottom-up implementation in two major phases: (1) core REST framework (config, router, middleware, controllers, services, API docs) — Tasks 1-13, and (2) XML service Groovy-to-Java conversion across all components — Tasks 14-18. Each task builds on the previous, with checkpoints to validate incremental progress.

## Tasks

- [x] 1 Set up project structure and core framework classes
  - [x] 1.1 Create `RestConfig` class with properties file loading and environment variable overrides
    - Create `org.moqui.rest.RestConfig` in `backend/runtime/component/rest-api/src/main/java/org/moqui/rest/`
    - Load from `rest-api.properties`, override with `REST_API_PORT`, `REST_API_HOST`, etc.
    - Include defaults: host=0.0.0.0, port=8080, minThreads=8, maxThreads=200, shutdownTimeout=30000
    - _Requirements: 12.1, 12.2, 12.3, 12.4_
  - [x]* 1.2 Write property test for RestConfig loading
    - **Property 18: Configuration loading with environment variable overrides**
    - **Validates: Requirements 12.1, 12.2, 12.3**
  - [x] 1.3 Create `RouteHandler` functional interface and `RouteMatch` class
    - Create `org.moqui.rest.RouteHandler` — `void handle(RequestContext ctx) throws Exception`
    - Create `org.moqui.rest.RouteMatch` — handler, pathParams map, isPublic flag
    - _Requirements: 2.1_
  - [x] 1.4 Create `Router` class with route registration and matching
    - Create `org.moqui.rest.Router` with `get()`, `post()`, `put()`, `delete()`, `publicRoute()`, `group()` methods
    - Implement path pattern matching with `{param}` extraction
    - Implement literal-over-parameter priority
    - Implement 405 detection (path matches but method doesn't)
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6_
  - [x]* 1.5 Write property tests for Router
    - **Property 1: Route matching with parameter extraction**
    - **Property 2: Literal route priority over parameterized routes**
    - **Property 3: Method Not Allowed response**
    - **Validates: Requirements 2.1, 2.2, 2.3, 2.4, 2.5, 2.6**
  - [x] 1.6 Create `RequestContext` class wrapping HttpServletRequest/Response
    - Create `org.moqui.rest.RequestContext` with pathParam(), queryParam(), bodyAs(), header(), userId(), clientIp()
    - Implement response helpers: json(), success(), error(), created(), noContent(), redirect()
    - Use Jackson ObjectMapper for JSON serialization/deserialization
    - Wire ApiResponse utility for response body construction
    - _Requirements: 8.1, 8.2, 8.3_

- [x] 2 Implement middleware pipeline
  - [x] 2.1 Create `Middleware` interface and pipeline executor
    - Create `org.moqui.rest.Middleware` interface with `boolean handle(RequestContext ctx, RouteMatch route)`
    - Create `MiddlewarePipeline` that chains middleware and short-circuits on rejection
    - _Requirements: 3.1, 3.2_
  - [x]* 2.2 Write property test for middleware short-circuit
    - **Property 5: Middleware short-circuit on rejection**
    - **Validates: Requirements 3.2**
  - [x] 2.3 Implement `SecurityHeadersMiddleware`
    - Delegate to existing `ApiErrorHandler.getSecurityHeaders()`
    - Add headers to every response
    - _Requirements: 10.1_
  - [x] 2.4 Implement `CorsMiddleware`
    - Handle OPTIONS preflight requests
    - Set Access-Control-Allow-Origin, Methods, Headers from RestConfig
    - _Requirements: 3.7_
  - [x] 2.5 Implement `RateLimitMiddleware`
    - Delegate to existing `RateLimiter.checkAndRecord()`
    - Key = userId (if authenticated) or clientIp
    - Return 429 with Retry-After header on rejection
    - _Requirements: 3.8, 10.3_
  - [x] 2.6 Implement `JwtAuthMiddleware`
    - Skip public routes (check RouteMatch.isPublic)
    - Extract Bearer token from Authorization header
    - Validate via JwtUtil.validateToken()
    - Set userId on RequestContext
    - Return 401 with TOKEN_EXPIRED or AUTHENTICATION_REQUIRED on failure
    - _Requirements: 3.3, 3.4, 3.5, 3.6_
  - [x]* 2.7 Write property tests for JWT middleware
    - **Property 6: Public routes skip JWT authentication**
    - **Property 7: JWT token validation populates request context**
    - **Property 8: JWT rejects invalid tokens with correct error codes**
    - **Validates: Requirements 3.3, 3.4, 3.5, 3.6**
  - [x] 2.8 Implement `SanitizationMiddleware`
    - Validate payload size via InputSanitizer.validatePayloadSize()
    - Return 413 if payload exceeds limit
    - _Requirements: 10.2, 10.5_

- [x] 3 Implement RestServer entry point with Jetty
  - [x] 3.1 Create `RestServer` class with Jetty setup
    - Create `org.moqui.rest.RestServer` with start() and stop() methods
    - Configure Jetty Server with QueuedThreadPool from RestConfig
    - Create JettyHandler that delegates to Router + MiddlewarePipeline
    - Implement graceful shutdown with configurable timeout
    - Log registered routes at startup
    - _Requirements: 1.1, 1.2, 1.4, 1.6, 1.7_
  - [x]* 3.2 Write property test for 404 on unregistered paths
    - **Property 4: Unknown resource returns 404**
    - **Validates: Requirements 1.5, 5.7, 6.4**

- [x] 4 Checkpoint - Core framework validation
  - Ensure all tests pass, ask the user if questions arise.
  - Verify: RestConfig loads correctly, Router matches routes, Middleware pipeline works, Jetty starts and serves requests

- [x] 5 Implement AuthController and AuthService
  - [x] 5.1 Create `AuthService` with login, refresh, logout logic
    - Create `org.moqui.rest.service.AuthService`
    - Implement login: find UserAccount via Entity Engine, check lockout, validate password, generate JWT via JwtUtil
    - Implement refresh: delegate to JwtUtil.refreshAccessToken()
    - Implement logout: delegate to JwtUtil.revokeToken()
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7_
  - [x]* 5.2 Write property tests for AuthService
    - **Property 9: Successful login returns tokens and resets failure counter**
    - **Property 10: Invalid login returns 401**
    - **Property 11: Token refresh and revocation round-trip**
    - **Validates: Requirements 4.1, 4.2, 4.4, 4.5, 4.6, 4.7**
  - [x] 5.3 Create `AuthController` with route registration
    - Create `org.moqui.rest.controller.AuthController`
    - Register public routes: POST /api/v1/auth/login, POST /api/v1/auth/refresh
    - Register protected route: POST /api/v1/auth/logout
    - Parse request bodies, delegate to AuthService, return Standardized_Response
    - _Requirements: 4.1, 4.2, 4.5, 4.6, 4.7_

- [x] 6 Implement EntityController and EntityCrudService
  - [x] 6.1 Create `EntityCrudService` with CRUD operations
    - Create `org.moqui.rest.service.EntityCrudService`
    - Implement list: build EntityFind with filters, orderBy, pagination, field selection
    - Implement get: find by PK (single or composite)
    - Implement create: delegate to Moqui implicit create service
    - Implement update: delegate to Moqui implicit update service
    - Implement delete: delegate to Moqui implicit delete service
    - Use InputSanitizer for entity name validation
    - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8, 5.9_
  - [x]* 6.2 Write property tests for EntityCrudService
    - **Property 12: Entity CRUD round-trip**
    - **Property 13: Entity listing with query parameters**
    - **Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5, 5.6**
  - [x] 6.3 Create `EntityController` with route registration
    - Create `org.moqui.rest.controller.EntityController`
    - Register routes: GET/POST /api/v1/entities/{entityName}, GET/PUT/DELETE /api/v1/entities/{entityName}/{entityId}
    - Parse query params for list operations, JSON body for create/update
    - _Requirements: 5.1, 5.3, 5.4, 5.5, 5.6_

- [x] 7 Implement ServiceController and ServiceInvocationService
  - [x] 7.1 Create `ServiceInvocationService` with sync/async invocation
    - Create `org.moqui.rest.service.ServiceInvocationService`
    - Implement invokeSync: validate service name, execute via ServiceFacade, return result
    - Implement invokeAsync: create AsyncJobRecord, execute async, return jobId
    - Implement getJobStatus: query AsyncJobRecord entity
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5, 6.6_
  - [x]* 7.2 Write property test for service invocation
    - **Property 14: Service invocation parameter pass-through**
    - **Validates: Requirements 6.1, 6.6**
  - [x] 7.3 Create `ServiceController` with route registration
    - Create `org.moqui.rest.controller.ServiceController`
    - Register routes: POST /api/v1/services/{serviceName}, GET /api/v1/services/jobs/{jobId}
    - Check X-Async header for async execution
    - _Requirements: 6.1, 6.2, 6.3_

- [x] 8 Checkpoint - Core API validation
  - Ensure all tests pass, ask the user if questions arise.
  - Verify: Auth endpoints work, Entity CRUD works, Service invocation works
  - Test with actual Moqui Entity Engine (integration test)

- [x] 9 Implement domain-specific controllers and services
  - [x] 9.1 Create `NovelService` and `NovelController`
    - Implement all novel operations: importText, importFile, analyzeStructure, CRUD, extractCharacters, scenes, episodes
    - Register routes under `/api/v1/novels`
    - Delegate to EntityCrudService for CRUD, ServiceInvocationService for business operations
    - _Requirements: 7.1, 7.4_
  - [x] 9.2 Create `CharacterService` and `CharacterController`
    - Implement: list, get, update, delete, lock, merge, relationships
    - Register routes under `/api/v1/characters`
    - _Requirements: 7.1, 7.5_
  - [x] 9.3 Create `SceneController` and `EpisodeController`
    - SceneController: get, update, approve, reanalyze under `/api/v1/scenes`
    - EpisodeController: get, update, delete, screenplay, adjust-boundaries under `/api/v1/episodes`
    - _Requirements: 7.1, 7.6, 7.7_
  - [x] 9.4 Create `ProjectController`, `CreditsController`, `WorkflowController`, `AssetController`
    - ProjectController: CRUD under `/api/v1/projects`
    - CreditsController: balance, consume, history under `/api/v1/credits`
    - WorkflowController: CRUD under `/api/v1/workflows` (delegate to EntityCrudService)
    - AssetController: CRUD under `/api/v1/assets` (delegate to EntityCrudService)
    - _Requirements: 7.1, 7.8, 7.9, 7.10_
  - [x]* 9.5 Write property test for standardized response format
    - **Property 15: Standardized response format with security headers**
    - **Property 16: Pagination metadata completeness**
    - **Validates: Requirements 8.1, 8.2, 8.3, 8.4, 10.1**

- [x] 10 Implement API documentation (Swagger UI)
  - [x] 10.1 Create `DocsController` with OpenAPI generation
    - Create `org.moqui.rest.controller.DocsController`
    - Generate OpenAPI 3.0 spec from Router's registered routes with metadata
    - Extend existing OpenApiGenerator with route-based generation
    - Serve spec at GET /api/v1/docs/openapi.json
    - _Requirements: 9.1, 9.3, 9.5_
  - [x] 10.2 Bundle and serve Swagger UI
    - Add swagger-ui-dist as a dependency (or bundle static files)
    - Serve Swagger UI HTML at GET /api/v1/docs
    - Serve static assets (JS, CSS) at GET /api/v1/docs/*
    - Configure Swagger UI to point to /api/v1/docs/openapi.json
    - _Requirements: 9.1, 9.2, 9.4_
  - [x] 10.3 Implement root URL redirect
    - GET / → redirect to /api/v1/docs
    - _Requirements: 1.3_
  - [x]* 10.4 Write property test for OpenAPI spec generation
    - **Property 17: OpenAPI spec contains all registered routes**
    - **Validates: Requirements 9.3**

- [x] 11 Checkpoint - Full API validation
  - Ensure all tests pass, ask the user if questions arise.
  - Verify: All domain endpoints respond correctly, Swagger UI loads, OpenAPI spec is complete

- [x] 12 Wire RestServer into application startup and clean up build
  - [x] 12.1 Create application entry point that starts RestServer
    - Create main class or integrate with existing Moqui startup
    - Initialize Moqui ExecutionContextFactory for Entity Engine access
    - Start RestServer with all controllers registered
    - _Requirements: 1.1, 1.4, 11.5_
  - [x] 12.2 Update build.gradle to exclude Groovy from REST API module
    - Remove Groovy compile dependency from rest-api component
    - Ensure XML service files are no longer loaded for API handling
    - Add jqwik test dependency
    - Add swagger-ui static resource dependency
    - _Requirements: 11.1, 11.2, 11.3_
  - [x] 12.3 Create `rest-api.properties` default configuration file
    - Include all configurable values with sensible defaults
    - Document each property with comments
    - _Requirements: 12.1, 12.2_

- [x] 13 Final checkpoint - End-to-end validation
  - Ensure all tests pass, ask the user if questions arise.
  - Verify: Server starts cleanly, shows only API docs, all endpoints work, no Groovy in API path
  - Run full test suite including property tests

- [x] 14 Convert REST API XML service embedded Groovy to Java (Phase A)
  - [x] 14.1 Convert AuthApiServices.xml scripts to AuthService.java
    - 3 scripts: login, refresh, logout → already covered by Task 5.1 AuthService
    - Verify AuthService.java implements exact same logic as Groovy scripts
    - Remove or mark AuthApiServices.xml as deprecated
    - _Requirements: 13.1, 13.2, 13.8_
  - [x] 14.2 Convert EntityApiServices.xml scripts to EntityCrudService.java
    - 5 scripts: list, get, create, update, delete → already covered by Task 6.1 EntityCrudService
    - Verify EntityCrudService.java implements exact same logic as Groovy scripts
    - Remove or mark EntityApiServices.xml as deprecated
    - _Requirements: 13.1, 13.2, 13.8_
  - [x] 14.3 Convert ServiceApiServices.xml scripts to ServiceInvocationService.java
    - 2 scripts: invoke, getJobStatus → already covered by Task 7.1 ServiceInvocationService
    - Verify ServiceInvocationService.java implements exact same logic as Groovy scripts
    - Remove or mark ServiceApiServices.xml as deprecated
    - _Requirements: 13.1, 13.2, 13.8_
  - [x] 14.4 Convert ScreenApiServices.xml script to ScreenService.java
    - 1 script: renderScreen → create `org.moqui.rest.service.ScreenService`
    - Convert Groovy screen rendering logic to Java
    - Remove or mark ScreenApiServices.xml as deprecated
    - _Requirements: 13.1, 13.2, 13.8_
  - [x] 14.5 Convert DocsApiServices.xml script to DocsController
    - 1 script: generateDocs → already covered by Task 10.1 DocsController
    - Verify DocsController implements exact same logic as Groovy script
    - Remove or mark DocsApiServices.xml as deprecated
    - _Requirements: 13.1, 13.2, 13.8_

- [x] 15 Convert Framework service embedded Groovy to Java (Phase B)
  - [x] 15.1 Convert SearchServices.xml embedded scripts to Java
    - Scan `framework/service/org/moqui/search/SearchServices.xml` for embedded Groovy
    - Create `org.moqui.impl.service.SearchServiceImpl.java` with equivalent Java logic
    - Update XML to use `<java>` delegation or replace entirely
    - _Requirements: 13.1, 13.3, 13.8, 13.9_
  - [x] 15.2 Convert SmsServices.xml embedded scripts to Java
    - Scan `framework/service/org/moqui/SmsServices.xml` for embedded Groovy
    - Create `org.moqui.impl.service.SmsServiceImpl.java` with equivalent Java logic
    - Update XML to use `<java>` delegation or replace entirely
    - _Requirements: 13.1, 13.3, 13.8, 13.9_
  - [x] 15.3 Convert standalone email Groovy scripts to Java
    - Convert framework email-related `.groovy` scripts to `org.moqui.impl.service.EmailServiceImpl.java`
    - Preserve email sending, template rendering, and notification logic
    - Remove standalone `.groovy` files after conversion
    - _Requirements: 13.1, 13.3, 13.10_
  - [x] 15.4 Verify framework services compile and pass tests
    - Build framework module, ensure no Groovy script execution in converted services
    - _Requirements: 13.11_

- [x] 16 Convert Mantle-USL business service embedded Groovy to Java (Phase C)
  - [x] 16.1 Convert ReconciliationServices.xml scripts to Java
    - Create `org.moqui.mantle.service.ReconciliationServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.2 Convert SubscriptionServices.xml scripts to Java
    - Create `org.moqui.mantle.service.SubscriptionServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.3 Convert StoreServices.xml scripts to Java
    - Create `org.moqui.mantle.service.StoreServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.4 Convert PromotionServices.xml scripts to Java
    - Create `org.moqui.mantle.service.PromotionServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.5 Convert WorkEffortServices.xml scripts to Java
    - Create `org.moqui.mantle.service.WorkEffortServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.6 Convert TimeServices.xml scripts to Java
    - Create `org.moqui.mantle.service.TimeServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.7 Convert ShipmentWorkServices.xml scripts to Java
    - Create `org.moqui.mantle.service.ShipmentWorkServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.8 Convert ProjectServices.xml scripts to Java
    - Create `org.moqui.mantle.service.ProjectServiceImpl.java`
    - Convert all embedded Groovy scripts preserving input/output contracts
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.9 Convert remaining Mantle-USL services with embedded Groovy
    - Scan all remaining mantle-usl XML service files for `<script>` blocks
    - Convert each to corresponding Java service class
    - _Requirements: 13.1, 13.4, 13.8, 13.9_
  - [x] 16.10 Convert standalone PartyServices.groovy to Java
    - Convert `mantle-usl/service/.../PartyServices.groovy` to `org.moqui.mantle.service.PartyServiceImpl.java`
    - Remove standalone `.groovy` file after conversion
    - _Requirements: 13.1, 13.4, 13.10_
  - [x] 16.11 Verify Mantle-USL services compile and pass tests
    - Build mantle-usl module, ensure no Groovy script execution in converted services
    - _Requirements: 13.11_

- [x] 17 Convert Component AI service embedded Groovy to Java (Phase D)
  - [x] 17.1 Convert HiveMind AI services scripts to Java
    - Create `org.moqui.hivemind.service.AiServiceImpl.java`
    - Convert mind map generation, text analysis scripts (3 scripts)
    - _Requirements: 13.1, 13.5, 13.8, 13.9_
  - [x] 17.2 Convert PopCommerce AI services scripts to Java
    - Create `org.moqui.popcommerce.service.AiServiceImpl.java`
    - Convert product recommendations, description optimization, inventory prediction, customer inquiry scripts (4 scripts)
    - _Requirements: 13.1, 13.6, 13.8, 13.9_
  - [x] 17.3 Convert MarbleERP AI services scripts to Java
    - Create `org.moqui.marble.service.AiServiceImpl.java`
    - Convert financial analysis, supply chain, demand forecast, business metrics scripts (4 scripts)
    - _Requirements: 13.1, 13.7, 13.8, 13.9_
  - [x] 17.4 Verify component AI services compile and pass tests
    - Build all component modules, ensure no Groovy script execution in converted services
    - _Requirements: 13.11_

- [x] 18 Final Groovy elimination checkpoint
  - Scan entire codebase for remaining `<script><![CDATA[` blocks in XML service files
  - Scan for remaining `.groovy` standalone script files (excluding ScreenForm.groovy and ScreenRenderImpl.groovy which are tracked as tech debt)
  - Verify full build passes: `.\gradlew.bat clean build`
  - Verify no Groovy script execution in service layer at runtime
  - Update build.gradle to remove Groovy dependencies where possible (except for 2 remaining .groovy source files)

- [x] 19 API discoverability consolidation (KSE 接管扩展)
  - [x] 19.1 Add unified API index endpoint
    - Add `GET /api/v1` to provide docs/openapi/health/metrics/services/entities/screens entry links
  - [x] 19.2 Add aggregated catalog endpoint
    - Add `GET /api/v1/catalog` to aggregate service/entity/screen component counts
  - [x] 19.3 Add route discovery endpoint
    - Add `GET /api/v1/routes` with query filters: `tag`, `publicOnly`
    - Expose method/path/public/tag for all registered routes
  - [x] 19.4 Add OpenAPI and regression coverage
    - Extend OpenAPI generator query params for `/api/v1/routes`
    - Extend endpoint and docs property tests for new discovery endpoints

## Notes

- Tasks marked with `*` are optional property-based tests that can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties
- Unit tests validate specific examples and edge cases
- Domain controllers (Task 9) can be implemented in parallel since they're independent
- The existing Java utilities (ApiResponse, ApiErrorHandler, InputSanitizer, RateLimiter, JwtUtil) are reused without modification
- Tasks 1-13: Pure REST framework (embedded Jetty, controllers, services, API docs)
- Tasks 14-18: XML service Groovy-to-Java conversion across all components
  - Phase A (Task 14): REST API services — largely covered by Tasks 5-7, this task verifies parity and removes XML files
  - Phase B (Task 15): Framework services (search, SMS, email)
  - Phase C (Task 16): Mantle-USL business services (30+ scripts, largest phase)
  - Phase D (Task 17): Component AI services (HiveMind, PopCommerce, MarbleERP)
  - Task 18: Final verification — scan for remaining Groovy, full build validation
- 2 Groovy source files (ScreenForm.groovy, ScreenRenderImpl.groovy) remain as tracked tech debt — not in scope for this spec

