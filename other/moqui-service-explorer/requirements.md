---
name: moqui-service-explorer
category: other
description: Template for Moqui Service Explorer
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

{{SPEC_NAME_TITLE}} 前端模块，为 331-poc ERP 现代化项目提供 Service 浏览和分析功能。用户可以按组件浏览 Service 目录、搜索 Service、查看 Service 定义详情（参数、认证、远程调用配置）。该模块遵循 Screen Explorer (22-00) 和 Entity Explorer (23-00) 的架构模式，复用已有的后端 Service Catalog REST API。

## Glossary

- **Service_Explorer**: Service 浏览和分析前端模块，包含列表视图和详情视图
- **Service_Catalog_API**: 后端 REST API 端点，提供 Service 目录和定义数据（`GET /api/v1/services`, `GET /api/v1/services/{serviceName}/definition`）
- **Service_Catalog_Store**: Pinia Store，管理 Service 列表、筛选、分页、详情的状态（文件名 `serviceCatalog.ts`，避免与已有 `serviceInvoker.ts` 冲突）
- **Service_Catalog_Browser**: Service 列表浏览组件，支持搜索、组件筛选、分页
- **Service_Detail_Panel**: Service 详情面板，展示基本信息（概览、入参、出参标签页）
- **Service_Summary**: Service 摘要数据，包含 serviceName、path、verb、noun、component、authenticate、allowRemote 等
- **Navigation_Store**: 全局导航 Pinia Store，管理侧边栏导航项和面板上下文

## Requirements

### Requirement 1: Service Catalog API 服务层

**User Story:** As a developer, I want a typed API service that communicates with the Service Catalog REST endpoints, so that the frontend can retrieve service catalog data reliably.

#### Acceptance Criteria

1. THE Service_Catalog_API_Service SHALL expose a `listServices` method that accepts optional component, q (keyword), pageIndex, pageSize, and includeParams parameters and returns typed ServiceListData
2. THE Service_Catalog_API_Service SHALL expose a `getServiceDefinition` method that accepts a serviceName parameter and returns typed ServiceSummary with includeParams=true
3. THE Service_Catalog_API_Service SHALL reuse the existing apiV1Client for all HTTP requests
4. WHEN the API returns an error response, THE Service_Catalog_API_Service SHALL propagate the typed ApiV1Result error to the caller

### Requirement 2: Service State Management

**User Story:** As a developer, I want a Pinia store that manages service data, filters, pagination, and loading states, so that components can reactively display service information.

#### Acceptance Criteria

1. THE Service_Catalog_Store SHALL maintain a list of ServiceSummary items, pagination metadata, and loading/error states for the list view
2. THE Service_Catalog_Store SHALL maintain currentService (ServiceSummary with params) and detail loading/error states for the detail view
3. THE Service_Catalog_Store SHALL provide a `fetchServices` action that calls Service_Catalog_API_Service.listServices with current filter and pagination state
4. THE Service_Catalog_Store SHALL provide a `fetchServiceDetail` action that calls Service_Catalog_API_Service.getServiceDefinition for a given serviceName
5. THE Service_Catalog_Store SHALL provide a `setFilter` action that updates component/keyword filters, resets pageIndex to 0, and re-fetches the service list
6. THE Service_Catalog_Store SHALL provide a `setPage` action that updates pageIndex and re-fetches the service list
7. WHEN a fetch action starts, THE Service_Catalog_Store SHALL set the corresponding loading flag to true and clear the previous error
8. WHEN a fetch action completes successfully, THE Service_Catalog_Store SHALL update the corresponding data and set loading to false
9. IF a fetch action fails, THEN THE Service_Catalog_Store SHALL set the corresponding error message and set loading to false

### Requirement 3: Service Type Definitions

**User Story:** As a developer, I want TypeScript type definitions that match the backend Service Catalog API response shapes, so that the frontend has compile-time type safety.

#### Acceptance Criteria

1. THE Service_Explorer type module SHALL define ServiceSummary with fields: component, serviceName, path, verb, noun (nullable), authenticate, allowRemote, inParams (optional string[]), outParams (optional string[])
2. THE Service_Explorer type module SHALL define ServiceListData with fields: services (ServiceSummary[]), pageIndex, pageSize, total, generatedAt, component (optional)
3. THE Service_Explorer type module SHALL define ServiceListParams with optional fields: component, q, pageIndex, pageSize, includeParams
4. THE Service_Explorer type module SHALL define ServicePaginationMeta with fields: total, pageIndex, pageSize

### Requirement 4: Service List View

**User Story:** As a user, I want to browse and search the service catalog with filtering and pagination, so that I can find services of interest.

#### Acceptance Criteria

1. WHEN the Service list view loads, THE Service_Explorer SHALL call Service_Catalog_Store.fetchServices to populate the service list
2. THE Service_Catalog_Browser component SHALL display a search input for keyword filtering and a dropdown for component filtering
3. WHEN a user types in the search input, THE Service_Catalog_Browser SHALL debounce the input and call Service_Catalog_Store.setFilter with the keyword
4. WHEN a user selects a component from the dropdown, THE Service_Catalog_Browser SHALL call Service_Catalog_Store.setFilter with the selected component
5. THE Service_Catalog_Browser SHALL display each ServiceSummary as a list item showing serviceName, component badge, verb badge, noun, and authenticate/allowRemote indicators
6. WHEN a user clicks a service list item, THE Service_Explorer SHALL navigate to the service detail route `/services/:serviceName`
7. WHILE Service_Catalog_Store.listLoading is true, THE Service_Catalog_Browser SHALL display a loading spinner
8. IF Service_Catalog_Store.listError is set, THEN THE Service_Catalog_Browser SHALL display the error message with a retry button
9. WHEN no services match the current filters, THE Service_Catalog_Browser SHALL display an empty state message
10. THE Service_Catalog_Browser SHALL display pagination controls and call Service_Catalog_Store.setPage when the user changes pages

### Requirement 5: Service Detail View

**User Story:** As a user, I want to view the full definition of a service including its parameters, so that I can understand the service interface.

#### Acceptance Criteria

1. WHEN the Service detail view loads with a route parameter serviceName, THE Service_Explorer SHALL call Service_Catalog_Store.fetchServiceDetail
2. THE Service_Detail_Panel SHALL display service basic information: serviceName, path, verb, noun, component, authenticate, allowRemote
3. THE Service_Detail_Panel SHALL provide tab navigation with tabs: 概览 (overview), 入参 (in-params), 出参 (out-params)
4. WHEN the 入参 tab is active, THE Service_In_Params_Panel component SHALL display all inParams as a list
5. WHEN the 出参 tab is active, THE Service_Out_Params_Panel component SHALL display all outParams as a list
6. WHILE Service_Catalog_Store.detailLoading is true, THE Service_Detail_Panel SHALL display a loading spinner
7. IF Service_Catalog_Store.detailError is set, THEN THE Service_Detail_Panel SHALL display the error message with a retry button
8. THE Service detail view SHALL provide a back navigation button that returns to the service list view

### Requirement 6: Routing and Navigation Integration

**User Story:** As a user, I want to access the Service Explorer from the sidebar navigation and navigate between list and detail views, so that the module is integrated into the application.

#### Acceptance Criteria

1. THE Router SHALL register route `/services` mapped to ServiceListView with meta `requiresAuth: true`
2. THE Router SHALL register route `/services/:serviceName` mapped to ServiceDetailView with meta `requiresAuth: true`
3. THE Navigation_Store SHALL include a 'services' NavId with route '/services', label 'Service 探索', and description '浏览和分析 Moqui Service 定义'
4. THE Navigation_Store SHALL include a services PanelContext with currentKeyword and currentComponent fields
5. WHEN a user clicks the Service 探索 navigation item, THE Service_Explorer SHALL navigate to the `/services` route

### Requirement 7: Error Handling and Edge Cases

**User Story:** As a user, I want graceful error handling when API calls fail or data is unavailable, so that the application remains usable.

#### Acceptance Criteria

1. IF the Service_Catalog_API returns a 404 for a service definition, THEN THE Service_Detail_Panel SHALL display a "Service 未找到" message
2. IF a network error occurs during any Service API call, THEN THE Service_Catalog_Store SHALL set a user-friendly error message
3. WHEN navigating away from the service detail view, THE Service_Catalog_Store SHALL clear the detail state via clearDetail action
