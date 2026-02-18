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

# Requirements Document

## Introduction

Phase 4 of the Moqui ERP modernization project. The goal is twofold: (1) completely replace the Moqui framework's HTTP layer (screen rendering, XML actions, Groovy script runner) with a pure Java REST API backend powered by embedded Jetty, and (2) convert ALL embedded Groovy scripts in XML service definitions across ALL components (REST API, Framework, Mantle-USL, HiveMind, PopCommerce, MarbleERP) to pure Java service classes. The backend starts up showing only API documentation and REST endpoints — no Groovy, no XML actions, no screen rendering. All existing `/api/v1/` endpoint contracts are preserved, and all business logic moves from XML-embedded Groovy scripts to pure Java.

## Glossary

- **REST_Server**: The embedded Jetty HTTP server that hosts all REST API endpoints and serves the Swagger UI documentation page
- **Router**: The component that maps incoming HTTP requests to the appropriate Controller method based on URL path and HTTP method
- **Controller**: A pure Java class that handles HTTP requests for a specific resource group (e.g., AuthController, NovelController), parses request parameters, and delegates to Service classes
- **Service_Layer**: Pure Java classes containing business logic, invoked by Controllers, that interact with the Repository layer for data access
- **Repository**: A data access abstraction that uses Moqui's Entity Engine (EntityFacade) to perform CRUD operations on entities
- **Middleware**: Request processing components that execute before Controllers, handling cross-cutting concerns such as authentication, rate limiting, CORS, and input sanitization
- **JWT_Filter**: A Middleware component that validates JWT tokens on protected endpoints and populates the request context with user identity
- **Standardized_Response**: The JSON response format `{ success: boolean, data?: object, meta?: object, error?: { code, message, details? } }` used by all endpoints
- **Entity_Engine**: Moqui's existing Java-based data access layer (EntityFacade) that handles database operations, entity definitions, and transactions
- **Swagger_UI**: The interactive API documentation page served at the root URL, allowing developers to explore and test all endpoints
- **Domain_Entity**: A business domain object (Novel, Character, Scene, Episode, Project, Credit, Workflow, Asset) managed through the API

## Requirements

### Requirement 1: Embedded Jetty REST Server

**User Story:** As a developer, I want the backend to start as a pure REST API server with embedded Jetty, so that the application serves only API endpoints and documentation without any legacy screen rendering or Groovy overhead.

#### Acceptance Criteria

1. WHEN the REST_Server starts, THE REST_Server SHALL bind to a configurable host and port (default `0.0.0.0:8080`)
2. WHEN the REST_Server starts, THE REST_Server SHALL log all registered API endpoint paths and HTTP methods to the console
3. WHEN a browser navigates to the root URL `/`, THE REST_Server SHALL redirect to the Swagger_UI documentation page at `/api/v1/docs`
4. THE REST_Server SHALL use embedded Jetty as the HTTP server without requiring an external servlet container
5. WHEN the REST_Server receives a request for an unregistered path, THE REST_Server SHALL return HTTP 404 with a Standardized_Response error body
6. WHEN the REST_Server shuts down, THE REST_Server SHALL gracefully close all active connections within a configurable timeout (default 30 seconds)
7. THE REST_Server SHALL support configuring thread pool size for request handling (default min 8, max 200)

### Requirement 2: Request Routing

**User Story:** As a developer, I want a clean routing system that maps URL paths and HTTP methods to controller methods, so that adding new endpoints is straightforward and type-safe.

#### Acceptance Criteria

1. THE Router SHALL support registering routes with path patterns including path parameters (e.g., `/api/v1/novels/{id}`)
2. THE Router SHALL match incoming requests by both URL path and HTTP method (GET, POST, PUT, DELETE)
3. WHEN a route with path parameters is matched, THE Router SHALL extract named path parameters and make them available to the Controller
4. WHEN multiple routes could match a request, THE Router SHALL prefer the most specific route (literal segments over parameters)
5. THE Router SHALL support grouping routes under a common prefix (e.g., all `/api/v1/novels/*` routes grouped together)
6. WHEN a route matches the path but not the HTTP method, THE Router SHALL return HTTP 405 Method Not Allowed with an `Allow` header listing valid methods

### Requirement 3: Middleware Pipeline

**User Story:** As a developer, I want a middleware pipeline for cross-cutting concerns, so that authentication, rate limiting, CORS, and input validation are handled consistently across all endpoints.

#### Acceptance Criteria

1. THE Middleware pipeline SHALL execute in a defined order: CORS → Rate Limiting → Authentication → Input Sanitization → Controller
2. WHEN any Middleware component rejects a request, THE Middleware pipeline SHALL short-circuit and return the error response without invoking subsequent middleware or the Controller
3. THE JWT_Filter SHALL skip authentication for routes explicitly marked as public (e.g., login, token refresh, API docs)
4. WHEN the JWT_Filter validates a token, THE JWT_Filter SHALL extract the user ID and roles and attach them to the request context
5. IF the JWT_Filter encounters an expired token, THEN THE JWT_Filter SHALL return HTTP 401 with error code `TOKEN_EXPIRED`
6. IF the JWT_Filter encounters an invalid or missing token on a protected route, THEN THE JWT_Filter SHALL return HTTP 401 with error code `AUTHENTICATION_REQUIRED`
7. THE CORS Middleware SHALL allow configurable origins, methods, and headers (default: allow all origins in development, restricted in production)
8. THE Rate Limiting Middleware SHALL use the existing RateLimiter class to enforce per-client request limits

### Requirement 4: Authentication Endpoints

**User Story:** As a user, I want to authenticate via pure Java REST endpoints, so that login, token refresh, and logout work without any Groovy or XML action processing.

#### Acceptance Criteria

1. WHEN a user sends POST `/api/v1/auth/login` with valid `{ username, password }`, THE Controller SHALL return a Standardized_Response containing `accessToken`, `refreshToken`, `expiresIn`, and `userId`
2. WHEN a user sends POST `/api/v1/auth/login` with invalid credentials, THE Controller SHALL return HTTP 401 with error code `AUTHENTICATION_REQUIRED` and message "Invalid username or password"
3. WHEN a user account has 5 or more consecutive failed login attempts, THE Controller SHALL return HTTP 401 with error code `ACCOUNT_LOCKED` and message "Account is locked due to too many failed login attempts"
4. WHEN a successful login occurs after previous failures, THE Controller SHALL reset the failed login counter to zero
5. WHEN a user sends POST `/api/v1/auth/refresh` with a valid refresh token, THE Controller SHALL return new `accessToken` and `refreshToken`
6. WHEN a user sends POST `/api/v1/auth/refresh` with an invalid or expired refresh token, THE Controller SHALL return HTTP 401 with error code `TOKEN_EXPIRED`
7. WHEN a user sends POST `/api/v1/auth/logout` with a valid token, THE Controller SHALL revoke the token and return `{ success: true }`

### Requirement 5: Entity CRUD Endpoints

**User Story:** As a frontend developer, I want generic entity CRUD endpoints implemented in pure Java, so that I can create, read, update, and delete any entity through a consistent REST interface.

#### Acceptance Criteria

1. WHEN a GET request is sent to `/api/v1/entities/{entityName}`, THE Controller SHALL return a paginated list of entities using the Entity_Engine
2. THE Entity CRUD endpoints SHALL support query parameters for filtering, sorting (`orderBy`), pagination (`pageIndex`, `pageSize`), and field selection (`fields`)
3. WHEN a GET request is sent to `/api/v1/entities/{entityName}/{id}`, THE Controller SHALL return the entity matching the primary key
4. WHEN a POST request is sent to `/api/v1/entities/{entityName}` with a JSON body, THE Controller SHALL create a new entity and return HTTP 201 with the created entity data
5. WHEN a PUT request is sent to `/api/v1/entities/{entityName}/{id}` with a JSON body, THE Controller SHALL update the entity and return the updated data
6. WHEN a DELETE request is sent to `/api/v1/entities/{entityName}/{id}`, THE Controller SHALL delete the entity and return HTTP 204
7. IF an entity name is invalid or not found, THEN THE Controller SHALL return HTTP 404 with error code `RESOURCE_NOT_FOUND`
8. IF entity validation fails, THEN THE Controller SHALL return HTTP 400 with error code `VALIDATION_ERROR` and field-level error details
9. THE Entity CRUD endpoints SHALL use the existing InputSanitizer to validate entity names before processing

### Requirement 6: Service Invocation Endpoints

**User Story:** As a frontend developer, I want to invoke Moqui services through pure Java REST endpoints, so that business logic executes without Groovy script processing.

#### Acceptance Criteria

1. WHEN a POST request is sent to `/api/v1/services/{serviceName}` with a JSON body, THE Controller SHALL invoke the named Moqui service synchronously and return the result
2. WHEN the `X-Async: true` header is present, THE Controller SHALL invoke the service asynchronously, return HTTP 202 with a `jobId`
3. WHEN a GET request is sent to `/api/v1/services/jobs/{jobId}`, THE Controller SHALL return the current status and result of the async job
4. IF a service name is invalid or not found, THEN THE Controller SHALL return HTTP 404 with error code `RESOURCE_NOT_FOUND`
5. THE Service Invocation endpoints SHALL use the existing InputSanitizer to validate service names before processing
6. THE Controller SHALL pass service parameters from the JSON request body to the Moqui ServiceFacade

### Requirement 7: Domain-Specific REST Endpoints

**User Story:** As a frontend developer, I want domain-specific REST endpoints (novels, characters, scenes, episodes, projects, credits, workflows, assets), so that the API surface matches the frontend's expected URL structure.

#### Acceptance Criteria

1. THE REST_Server SHALL expose domain-specific endpoints that map to the frontend's expected API contract (e.g., `POST /api/v1/novels/import-text`, `GET /api/v1/novels/{id}/scenes`)
2. WHEN a domain endpoint is called, THE Controller SHALL delegate to the appropriate Service_Layer method which uses the Entity_Engine or ServiceFacade
3. THE domain endpoints SHALL preserve the same request and response contracts as defined in the existing frontend API client (Spec 08-00)
4. THE domain endpoints SHALL include all Novel endpoints: import-text, import-file, analyze-structure, CRUD, extract-characters, scenes, episodes
5. THE domain endpoints SHALL include all Character endpoints: list, get, update, delete, lock, merge, relationships
6. THE domain endpoints SHALL include all Scene endpoints: get, update, approve, reanalyze
7. THE domain endpoints SHALL include all Episode endpoints: get, update, delete, screenplay, adjust-boundaries
8. THE domain endpoints SHALL include Credits endpoints: balance, consume, history
9. THE domain endpoints SHALL include Project CRUD endpoints
10. THE domain endpoints SHALL include Workflow and Asset CRUD endpoints

### Requirement 8: Standardized Response Format

**User Story:** As a frontend developer, I want all API responses to follow the standardized format, so that the frontend API client can parse responses consistently.

#### Acceptance Criteria

1. THE REST_Server SHALL return all success responses in the format `{ success: true, data: <payload>, meta?: <metadata> }`
2. THE REST_Server SHALL return all error responses in the format `{ success: false, error: { code: <string>, message: <string>, details?: [{ field, message }] } }`
3. THE REST_Server SHALL set the `Content-Type` header to `application/json; charset=utf-8` for all responses
4. WHEN returning paginated results, THE REST_Server SHALL include `meta` with `totalCount`, `pageIndex`, `pageSize`, and `pageMaxIndex`
5. THE REST_Server SHALL use the existing ApiResponse utility class for constructing all response bodies

### Requirement 9: API Documentation

**User Story:** As a developer, I want interactive API documentation served at startup, so that I can explore and test all available endpoints.

#### Acceptance Criteria

1. WHEN the REST_Server starts, THE REST_Server SHALL serve Swagger UI at `/api/v1/docs`
2. THE Swagger_UI SHALL display all registered endpoints with their HTTP methods, parameters, request bodies, and response schemas
3. THE REST_Server SHALL generate an OpenAPI 3.0 specification from the registered routes and serve it at `/api/v1/docs/openapi.json`
4. THE Swagger_UI SHALL support sending test requests directly from the documentation page with JWT token authentication
5. THE OpenAPI specification SHALL include example request and response bodies for each endpoint

### Requirement 10: Security

**User Story:** As a security engineer, I want the pure REST backend to maintain all existing security measures, so that the migration does not introduce vulnerabilities.

#### Acceptance Criteria

1. THE REST_Server SHALL include security headers on all responses: `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `X-XSS-Protection: 1; mode=block`
2. THE REST_Server SHALL validate and sanitize all input using the existing InputSanitizer class
3. THE REST_Server SHALL enforce rate limiting using the existing RateLimiter class
4. THE REST_Server SHALL sanitize error messages in production mode using the existing ApiErrorHandler class
5. THE REST_Server SHALL enforce request payload size limits (default 1MB) using the existing InputSanitizer validation
6. THE REST_Server SHALL log all authentication attempts (success and failure) with client IP and timestamp

### Requirement 11: Groovy and Legacy Removal

**User Story:** As a developer, I want all Groovy dependencies and XML action processing removed from the API path, so that the backend runs as pure Java with faster startup and smaller footprint.

#### Acceptance Criteria

1. THE REST_Server SHALL handle all API requests through pure Java Controllers and Services without invoking Groovy scripts
2. THE REST_Server SHALL not use Moqui's XmlActions, GroovyScriptRunner, or GString template processing for API request handling
3. THE build configuration SHALL exclude Groovy runtime libraries from the REST API module's compile classpath
4. WHEN the REST_Server starts, THE REST_Server SHALL not initialize Moqui's screen rendering subsystem
5. THE REST_Server SHALL retain Moqui's Entity Engine and ServiceFacade as Java dependencies for data access and service execution

### Requirement 13: XML Service Embedded Groovy to Java Conversion

**User Story:** As a developer, I want ALL embedded Groovy scripts in XML service definitions across ALL components converted to pure Java service classes, so that the entire backend runs without any Groovy script execution.

#### Acceptance Criteria

1. ALL embedded `<script><![CDATA[...]]></script>` blocks in XML service files SHALL be replaced with equivalent pure Java service class methods
2. THE conversion SHALL cover REST API services: AuthApiServices.xml (3 scripts), EntityApiServices.xml (5 scripts), ServiceApiServices.xml (2 scripts), ScreenApiServices.xml (1 script), DocsApiServices.xml (1 script)
3. THE conversion SHALL cover Framework services: SearchServices.xml, SmsServices.xml, and 3 standalone email Groovy scripts in `framework/service/`
4. THE conversion SHALL cover Mantle-USL business services: ReconciliationServices, SubscriptionServices, StoreServices, PromotionServices, WorkEffortServices, TimeServices, ShipmentWorkServices, ProjectServices (30+ scripts)
5. THE conversion SHALL cover HiveMind AI services: mind map generation, text analysis (3 scripts)
6. THE conversion SHALL cover PopCommerce AI services: product recommendations, description optimization, inventory prediction, customer inquiry (4 scripts)
7. THE conversion SHALL cover MarbleERP AI services: financial analysis, supply chain, demand forecast, business metrics (4 scripts)
8. EACH converted Java service class SHALL preserve the exact same input/output parameter contracts as the original XML service definition
9. THE XML service files SHALL be updated to use `<java method="..." class="..."/>` invocation instead of embedded `<script>` blocks, OR the XML service files SHALL be replaced entirely by Java service classes registered programmatically
10. ALL standalone `.groovy` script files (framework email scripts, mantle-usl party script) SHALL be converted to equivalent Java classes
11. AFTER conversion, the backend SHALL compile and pass all existing tests without Groovy script execution in the service layer

### Requirement 12: Configuration and Environment

**User Story:** As a developer, I want the server to be configurable through environment variables and configuration files, so that I can adjust settings for different deployment environments.

#### Acceptance Criteria

1. THE REST_Server SHALL read configuration from a properties file (default `rest-api.properties`) with environment variable overrides
2. THE configuration SHALL include: server port, host, thread pool sizes, JWT secret, rate limit thresholds, CORS allowed origins, production mode flag
3. WHEN an environment variable is set (e.g., `REST_API_PORT`), THE REST_Server SHALL use the environment variable value over the properties file value
4. THE REST_Server SHALL log the active configuration values (excluding secrets) at startup
