---
name: architecture-restructuring
category: other
description: Template for Architecture Restructuring
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

Spec 10-00 是 331-poc Moqui ERP 渐进式现代化项目的第五阶段。目标是对后端架构进行系统性评估和重构：移除不再需要的 Moqui 遗留组件（XML Screen 渲染引擎、WAR 打包、Groovy 编译链路、MoquiStart 启动器），将构建系统从 Gradle Groovy DSL 迁移到 Maven（纯 Java 项目的标准选择），并将 REST API 从 `runtime/component/rest-api` 子模块提升为顶层项目入口。最终目标：`java -jar app.jar` 即可启动，显示 API 文档，提供 REST 端点——类似 FastAPI 的开发体验。

## Glossary

- **REST_Backend**: 基于 Embedded Jetty 的纯 Java REST API 后端，包含 Router、Middleware、Controller、Service 各层
- **Entity_Engine**: Moqui 的 Java 数据访问层（EntityFacade），负责数据库 CRUD、实体定义、事务管理
- **Service_Facade**: Moqui 的服务调用框架（ServiceFacade），负责业务服务的注册和调用
- **ECFI**: ExecutionContextFactoryImpl，Moqui 的核心工厂类，负责初始化 Entity Engine、Service Facade、Cache、Security 等子系统
- **Build_System**: 项目的构建工具链，当前为 Gradle Groovy DSL，目标迁移到 Maven
- **Screen_Engine**: Moqui 的 XML Screen 渲染引擎，包括 ScreenForm.groovy、ScreenRenderImpl.groovy、FreeMarker 模板处理，在纯 REST 架构中不再需要
- **WAR_Packaging**: Moqui 的可执行 WAR 打包机制，包括 MoquiStart ClassLoader、execlib 目录、WEB-INF 结构
- **Lightweight_Bootstrap**: 新的轻量级启动流程，仅初始化 Entity Engine + Service Facade + Security + Cache，跳过 Screen Engine 和 Webapp 组件
- **Dependency_Facade**: 对 Moqui 核心依赖（Entity Engine、Service Facade、Security、Cache、Transaction）的抽象接口层

## Requirements

### Requirement 1: 依赖审计与组件分类

**User Story:** As a developer, I want a clear classification of which Moqui components are required vs removable, so that I can safely strip the framework down to only what the REST API needs.

#### Acceptance Criteria

1. THE Build_System SHALL include only the Moqui framework modules that the REST_Backend actively uses at runtime: Entity_Engine, Service_Facade, CacheFacade, TransactionFacade, UserFacade, and SecurityFacade
2. THE Build_System SHALL exclude Screen_Engine related classes (ScreenForm, ScreenRenderImpl, ScreenDefinition, ScreenUrlInfo) from the compilation and packaging pipeline
3. THE Build_System SHALL exclude FreeMarker template processing dependencies from the runtime classpath when Screen_Engine is not present
4. THE Build_System SHALL exclude Groovy runtime libraries (groovy, groovy-json, groovy-xml, groovy-templates) from the runtime classpath

### Requirement 2: Screen Engine 移除

**User Story:** As a developer, I want the XML Screen rendering engine completely removed, so that the backend contains zero screen-rendering code and the remaining 2 Groovy files are eliminated.

#### Acceptance Criteria

1. WHEN the REST_Backend starts, THE REST_Backend SHALL operate without loading any Screen_Engine classes
2. THE Build_System SHALL archive ScreenForm.groovy and ScreenRenderImpl.groovy to `docs/legacy-screen-engine/` for future frontend reference, and remove them from the compilation source set
3. THE Build_System SHALL remove the Groovy compilation plugin and joint compilation configuration from the build pipeline
4. WHEN any code references a removed Screen_Engine class, THE Build_System SHALL produce a compile-time error rather than a runtime error
5. THE REST_Backend SHALL retain all existing REST API endpoint functionality after Screen_Engine removal
6. THE Build_System SHALL archive Screen XML definitions, FreeMarker templates, and related rendering resources to `docs/legacy-screen-engine/` with a README index for future frontend development reference

### Requirement 3: WAR 打包移除与 JAR 化

**User Story:** As a developer, I want the application packaged as a simple executable JAR instead of a WAR, so that deployment is `java -jar app.jar` without needing a servlet container or custom ClassLoader.

#### Acceptance Criteria

1. THE Build_System SHALL produce an executable JAR file containing all runtime dependencies (fat JAR or shaded JAR)
2. THE Build_System SHALL configure the JAR manifest with `Main-Class` pointing to `RestApplication`
3. WHEN a user runs `java -jar app.jar`, THE REST_Backend SHALL start the Embedded Jetty server and begin serving REST endpoints
4. THE Build_System SHALL remove MoquiStart.java and the `start` source set from the build pipeline
5. THE Build_System SHALL remove the WAR plugin and all WAR-related build configuration
6. THE Build_System SHALL remove the `execWar` source set and `execWarRuntimeOnly` dependency configuration

### Requirement 4: 构建系统迁移

**User Story:** As a developer, I want the build system migrated from Gradle Groovy DSL to Maven, so that the pure Java project uses the most standard and widely-supported build tool.

#### Acceptance Criteria

1. THE Build_System SHALL use Maven as the build tool with a standard `pom.xml` configuration
2. THE Build_System SHALL configure Java 21 as the source and target compilation version
3. THE Build_System SHALL preserve all existing runtime dependencies (Jetty, Jackson, Shiro, H2, SLF4J/Log4j2, JWT, etc.) with identical version numbers
4. WHEN running `mvn clean package`, THE Build_System SHALL produce a working executable JAR
5. WHEN running `mvn test`, THE Build_System SHALL execute all unit tests and property-based tests
6. THE Build_System SHALL remove all Gradle files (build.gradle, settings.gradle, gradlew, gradle/) from the project after successful Maven migration
7. THE Build_System SHALL configure the Maven Shade Plugin or Spring Boot Maven Plugin to produce the fat JAR

### Requirement 5: 轻量级启动流程

**User Story:** As a developer, I want a lightweight bootstrap that initializes only the required Moqui subsystems, so that startup is fast and doesn't load unnecessary components.

#### Acceptance Criteria

1. WHEN the Lightweight_Bootstrap initializes, THE Lightweight_Bootstrap SHALL create an ECFI instance that loads Entity_Engine, Service_Facade, CacheFacade, TransactionFacade, and SecurityFacade
2. WHEN the Lightweight_Bootstrap initializes, THE Lightweight_Bootstrap SHALL skip initialization of Screen_Engine, WebApp components, and FreeMarker template engine
3. WHEN the Lightweight_Bootstrap completes initialization, THE REST_Backend SHALL log the startup time in milliseconds
4. IF the Lightweight_Bootstrap fails to initialize any required subsystem, THEN THE Lightweight_Bootstrap SHALL log a descriptive error message and exit with a non-zero status code
5. WHEN the Lightweight_Bootstrap initializes, THE Lightweight_Bootstrap SHALL read configuration from a single properties file or environment variables, without requiring MoquiDevConf.xml or MoquiProductionConf.xml for REST-only operation
6. THE Lightweight_Bootstrap SHALL support the existing Moqui configuration files (MoquiDevConf.xml) as a fallback for backward compatibility

### Requirement 6: 项目结构重组

**User Story:** As a developer, I want the REST API to be the top-level project entry point with a standard Java project layout, so that the project structure is intuitive and follows industry conventions.

#### Acceptance Criteria

1. THE project SHALL use a standard Maven directory layout: `src/main/java`, `src/main/resources`, `src/test/java`, `src/test/resources`
2. THE REST_Backend source code SHALL be located at the project root level, not nested under `runtime/component/rest-api`
3. THE Moqui framework source code SHALL be included as a module or dependency, not as the top-level project
4. THE project SHALL preserve the `runtime/` directory structure for entity definitions, service definitions, and configuration files that the Entity_Engine requires
5. WHEN the project is opened in an IDE, THE project SHALL be recognized as a standard Maven Java project with correct source roots

### Requirement 7: 运行时配置简化

**User Story:** As a developer, I want a simplified configuration system that uses standard properties files and environment variables, so that configuring the application doesn't require understanding Moqui's XML configuration hierarchy.

#### Acceptance Criteria

1. THE REST_Backend SHALL load configuration from `application.properties` at the classpath root
2. THE REST_Backend SHALL support environment variable overrides for all configuration properties using uppercase underscore convention (e.g., `DB_URL` overrides `db.url`)
3. THE REST_Backend SHALL provide sensible defaults for all configuration properties so that the application starts with zero configuration in development mode
4. WHEN a required external resource (database, cache) is unavailable, THE REST_Backend SHALL log a clear error message identifying the missing resource
5. THE REST_Backend SHALL support configuring database connection properties (URL, username, password, pool size) through the unified configuration system

### Requirement 8: 遗留组件清理

**User Story:** As a developer, I want all unused Moqui components and their build configurations removed, so that the codebase is clean and maintainable.

#### Acceptance Criteria

1. THE Build_System SHALL remove build configurations for components not used by the REST_Backend: webroot, SimpleScreens, PopCommerce, MarbleERP, HiveMind
2. THE Build_System SHALL retain Mantle-UDM entity definitions that are referenced by the REST_Backend's Entity_Engine
3. THE Build_System SHALL retain Mantle-USL service definitions that are invoked by the REST_Backend's Service_Facade
4. WHEN a component is removed, THE Build_System SHALL verify that no compile-time or runtime dependency exists from the REST_Backend to that component
5. THE project SHALL remove the `component.xml` auto-discovery mechanism and use explicit module declarations instead

### Requirement 9: 构建验证与回归测试

**User Story:** As a developer, I want automated verification that the restructured project builds correctly and all existing functionality works, so that the migration doesn't introduce regressions.

#### Acceptance Criteria

1. WHEN the migration is complete, THE Build_System SHALL compile all source files with zero errors and zero warnings
2. WHEN the migration is complete, THE REST_Backend SHALL pass all existing unit tests and property-based tests
3. WHEN the REST_Backend starts after migration, THE REST_Backend SHALL successfully initialize the Entity_Engine and connect to the configured database
4. WHEN the REST_Backend starts after migration, THE REST_Backend SHALL serve the Swagger UI documentation page at `/api/v1/docs`
5. WHEN the REST_Backend starts after migration, THE REST_Backend SHALL respond to all previously registered API endpoints with correct HTTP status codes
6. THE Build_System SHALL produce a build artifact (JAR) that is smaller than the current WAR artifact
