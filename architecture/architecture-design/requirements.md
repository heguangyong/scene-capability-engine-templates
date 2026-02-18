---
name: architecture-design
category: other
description: Template for Architecture Design
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document: Moqui Modernization {{SPEC_NAME_TITLE}}

## Introduction

This specification defines the architecture design for modernizing the Moqui ERP system while preserving its excellent Java framework foundation. The modernization focuses on replacing problematic components (Groovy code, XML service definitions, frontend-backend coupling) while keeping the proven assets (Entity Engine, Service Engine, Component System, Page Rendering capabilities).

**Strategic Approach**: Progressive modernization, not complete rebuild. We preserve what works and modernize what doesn't.

## Glossary

- **Moqui_Framework**: The core Java framework including Entity Engine, Service Engine, Transaction Management, Cache System, and Security Framework
- **Component_System**: The XML-based screen definition system including Forms, Widgets, Sections, and component libraries (SimpleScreens, HiveMind, etc.)
- **Rendering_Engine**: The current backend rendering system using FreeMarker templates and Screen Macros that generates HTML/CSV/XML/QVT outputs
- **REST_API_Layer**: New RESTful API layer built on top of Moqui framework, replacing XML service definitions
- **Groovy_Services**: Current service implementations written in Groovy scripts (88 files in framework/src/main/groovy/)
- **Java_Services**: New pure Java service implementations replacing Groovy scripts
- **Frontend_Separation**: Complete decoupling of frontend (Vue3/Electron) from backend via REST API
- **Migration_Path**: Gradual, incremental migration strategy with minimal disruption
- **OpenAPI_Specification**: Swagger/OpenAPI 3.0 standard for REST API documentation and contract definition

## Requirements

### Requirement 1: Preserve Moqui Framework Foundation

**User Story:** As a system architect, I want to preserve the excellent Moqui Java framework, so that we don't lose proven, stable functionality.

#### Acceptance Criteria

1. THE Architecture SHALL retain the Moqui Entity Engine without modification
2. THE Architecture SHALL retain the Moqui Service Engine (Java components) without modification
3. THE Architecture SHALL retain the Moqui Transaction Management system without modification
4. THE Architecture SHALL retain the Moqui Cache System (Hazelcast) without modification
5. THE Architecture SHALL retain the Moqui Database Abstraction layer without modification
6. THE Architecture SHALL retain the Moqui Security Framework without modification
7. WHEN evaluating framework components, THE Architecture SHALL prioritize preservation over replacement

### Requirement 2: Preserve Component and Page System

**User Story:** As a system architect, I want to preserve the mature component and page system, so that we retain the compact business structure and proven UI component libraries.

#### Acceptance Criteria

1. THE Architecture SHALL retain XML Screen definitions without modification
2. THE Architecture SHALL retain Form/Widget/Section component definitions without modification
3. THE Architecture SHALL retain component libraries (SimpleScreens, HiveMind, MarbleERP, PopCommerce) without modification
4. THE Architecture SHALL retain the component organization structure without modification
5. THE Architecture SHALL retain module division and data models without modification
6. WHEN designing frontend integration, THE Architecture SHALL leverage existing component definitions

### Requirement 3: Design REST API Layer on Moqui Foundation

**User Story:** As a system architect, I want to design a REST API layer on top of the existing Moqui framework, so that we can achieve frontend-backend separation without replacing the core framework.

#### Acceptance Criteria

1. THE REST_API_Layer SHALL be built on top of Moqui_Framework, not replace it
2. THE REST_API_Layer SHALL expose Moqui services through RESTful endpoints
3. THE REST_API_Layer SHALL follow OpenAPI 3.0 specification standards
4. THE REST_API_Layer SHALL support JSON request and response formats
5. THE REST_API_Layer SHALL integrate with Moqui's authentication and authorization system
6. THE REST_API_Layer SHALL leverage Moqui's transaction management
7. THE REST_API_Layer SHALL leverage Moqui's entity engine for data access
8. WHEN designing API endpoints, THE Architecture SHALL follow REST best practices (resource-based URLs, HTTP verbs, status codes)

### Requirement 4: Replace Groovy with Pure Java

**User Story:** As a developer, I want all Groovy code replaced with pure Java, so that we have a consistent, maintainable, and performant codebase.

#### Acceptance Criteria

1. THE Architecture SHALL define a strategy for converting all 88 Groovy service files to Java
2. THE Architecture SHALL define Java class structures for service implementations
3. THE Architecture SHALL define dependency injection patterns for Java services
4. THE Architecture SHALL define testing strategies for Java services
5. WHEN converting Groovy to Java, THE Architecture SHALL preserve business logic semantics
6. WHEN converting Groovy to Java, THE Architecture SHALL improve type safety
7. WHEN converting Groovy to Java, THE Architecture SHALL maintain or improve performance

### Requirement 5: Replace XML Service Definitions with OpenAPI

**User Story:** As an API developer, I want XML service definitions replaced with OpenAPI specifications, so that we have modern, standard API documentation and contract definitions.

#### Acceptance Criteria

1. THE Architecture SHALL define OpenAPI 3.0 specifications for all service endpoints
2. THE Architecture SHALL define request/response schemas using JSON Schema
3. THE Architecture SHALL define authentication and authorization schemes in OpenAPI
4. THE Architecture SHALL define error response formats and status codes
5. THE Architecture SHALL provide API documentation generation from OpenAPI specs
6. WHEN defining API contracts, THE Architecture SHALL follow RESTful design principles
7. WHEN migrating from XML services, THE Architecture SHALL maintain functional equivalence

### Requirement 6: Design Frontend-Backend Separation

**User Story:** As a system architect, I want complete frontend-backend separation, so that frontend and backend can evolve independently and scale separately.

#### Acceptance Criteria

1. THE Architecture SHALL define clear separation between frontend (Vue3/Electron) and backend (Moqui + REST API)
2. THE Architecture SHALL define communication exclusively through REST API
3. THE Architecture SHALL eliminate backend HTML rendering responsibilities
4. THE Architecture SHALL define frontend state management patterns
5. THE Architecture SHALL define frontend routing and navigation patterns
6. WHEN separating frontend and backend, THE Architecture SHALL ensure no direct database access from frontend
7. WHEN separating frontend and backend, THE Architecture SHALL define clear API boundaries

### Requirement 7: Design Rendering Engine Migration to Frontend

**User Story:** As a system architect, I want to migrate the rendering engine to the frontend, so that we can leverage modern frontend frameworks while preserving rendering capabilities.

#### Acceptance Criteria

1. THE Architecture SHALL define how Screen Render engine capabilities migrate to Vue3
2. THE Architecture SHALL define how FreeMarker template logic translates to Vue3 components
3. THE Architecture SHALL define how Screen Macros (DefaultScreenMacros.*.ftl) map to Vue3 composables or mixins
4. THE Architecture SHALL define how multi-format output (HTML, CSV, XML, QVT) is supported in frontend
5. THE Architecture SHALL define integration patterns between XML Screen definitions and Vue3 components
6. WHEN migrating rendering engine, THE Architecture SHALL preserve all current rendering capabilities
7. WHEN migrating rendering engine, THE Architecture SHALL improve rendering performance

### Requirement 8: Research Rendering Engine Feasibility

**User Story:** As a system architect, I want feasibility research on rendering engine migration, so that we understand risks, challenges, and implementation approaches before committing.

#### Acceptance Criteria

1. THE Research SHALL analyze current Moqui rendering engine architecture and capabilities
2. THE Research SHALL evaluate Vue3/Electron integration approaches
3. THE Research SHALL identify technical challenges and risks
4. THE Research SHALL propose prototype implementations for validation
5. THE Research SHALL estimate effort and complexity
6. THE Research SHALL recommend go/no-go decision criteria
7. WHEN conducting research, THE Analysis SHALL include code examples and proof-of-concept implementations

### Requirement 9: Design Gradual Migration Strategy

**User Story:** As a project manager, I want a gradual migration strategy, so that we can minimize risk, maintain system stability, and deliver value incrementally.

#### Acceptance Criteria

1. THE Migration_Path SHALL define clear phases with incremental deliverables
2. THE Migration_Path SHALL define coexistence strategy for old and new implementations
3. THE Migration_Path SHALL define rollback procedures for each phase
4. THE Migration_Path SHALL define testing and validation gates between phases
5. THE Migration_Path SHALL prioritize high-value, low-risk migrations first
6. THE Migration_Path SHALL define success criteria for each phase
7. WHEN defining migration phases, THE Strategy SHALL ensure system remains operational throughout

### Requirement 10: Design Integration with Vue3/Electron

**User Story:** As a frontend developer, I want clear integration patterns with Vue3/Electron, so that I can build a modern, responsive desktop application on top of the REST API.

#### Acceptance Criteria

1. THE Architecture SHALL define Vue3 component structure and organization
2. THE Architecture SHALL define Electron main/renderer process communication patterns
3. THE Architecture SHALL define API client architecture (HTTP client, error handling, retry logic)
4. THE Architecture SHALL define state management approach (Vuex/Pinia)
5. THE Architecture SHALL define routing and navigation patterns (Vue Router)
6. THE Architecture SHALL define authentication flow in Electron environment
7. WHEN integrating with Vue3/Electron, THE Architecture SHALL follow Vue3 composition API best practices

### Requirement 11: Define Risk Mitigation Strategy

**User Story:** As a project manager, I want a comprehensive risk mitigation strategy, so that we can proactively address potential issues and ensure project success.

#### Acceptance Criteria

1. THE Architecture SHALL identify technical risks and mitigation approaches
2. THE Architecture SHALL identify business continuity risks and mitigation approaches
3. THE Architecture SHALL identify performance risks and mitigation approaches
4. THE Architecture SHALL identify security risks and mitigation approaches
5. THE Architecture SHALL define contingency plans for high-impact risks
6. THE Architecture SHALL define monitoring and early warning indicators
7. WHEN identifying risks, THE Strategy SHALL prioritize based on likelihood and impact

### Requirement 12: Define Quality and Testing Strategy

**User Story:** As a quality engineer, I want a comprehensive testing strategy, so that we can ensure the modernized system maintains quality and correctness throughout migration.

#### Acceptance Criteria

1. THE Architecture SHALL define unit testing approach for Java services
2. THE Architecture SHALL define integration testing approach for REST API
3. THE Architecture SHALL define end-to-end testing approach for frontend-backend integration
4. THE Architecture SHALL define property-based testing approach for critical business logic
5. THE Architecture SHALL define performance testing approach and benchmarks
6. THE Architecture SHALL define regression testing strategy during migration
7. WHEN defining testing strategy, THE Architecture SHALL ensure automated test coverage for all critical paths
