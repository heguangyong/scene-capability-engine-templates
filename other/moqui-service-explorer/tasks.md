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

# Implementation Plan: {{SPEC_NAME_TITLE}}

## Overview

按 Entity Explorer 模式实现 Service Explorer 前端模块：types → service → store → components → views → routes → navigation。每层增量构建，逐步集成。

## Tasks

- [x] 1. Create Service type definitions
  - [x] 1.1 Create `frontend/src/renderer/types/service.ts` with all interfaces: ServiceSummary, ServiceListParams, ServicePaginationMeta, ServiceListData
    - Match backend ServiceCatalogService response shapes exactly
    - Use `total` (not `totalCount`), `q` (not `keyword`), no `pageMaxIndex`
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [x] 2. Create Service Catalog API service
  - [x] 2.1 Create `frontend/src/renderer/services/serviceCatalogApiService.ts` with ServiceCatalogApiService class
    - Implement listServices, getServiceDefinition methods
    - Reuse apiV1Client, follow entityCatalogApiService.ts pattern
    - listServices: `GET /services` with component, q, pageIndex, pageSize, includeParams params
    - getServiceDefinition: `GET /services/{serviceName}/definition?includeParams=true`
    - _Requirements: 1.1, 1.2, 1.3_
  - [x]* 2.2 Write property test for API error propagation
    - **Property 1: API error propagation**
    - **Validates: Requirements 1.4**

- [x] 3. Create Service Catalog Pinia Store
  - [x] 3.1 Create `frontend/src/renderer/stores/serviceCatalog.ts` with useServiceCatalogStore
    - State: services, pagination, listLoading, listError, currentComponent, currentKeyword, currentPageIndex, currentPageSize, currentService, detailLoading, detailError
    - Getters: hasServices, hasDetail, totalPages (computed from total/pageSize), hasNextPage, hasPreviousPage
    - Actions: fetchServices, fetchServiceDetail, setFilter, setPage, clearDetail
    - Follow entity.ts store pattern, adapt for Service API differences (total vs totalCount, q vs keyword)
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9_
  - [x]* 3.2 Write property test for fetchServices passes current state
    - **Property 2: fetchServices passes current filter/pagination state**
    - **Validates: Requirements 2.3**
  - [x]* 3.3 Write property test for setFilter resets pageIndex
    - **Property 3: setFilter always resets pageIndex to 0**
    - **Validates: Requirements 2.5**
  - [x]* 3.4 Write property test for fetch action lifecycle
    - **Property 4: Fetch action lifecycle state transitions**
    - **Validates: Requirements 2.7, 2.8, 2.9, 7.2**

- [x] 4. Checkpoint - Types, Service, Store
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5. Create Service list components and view
  - [x] 5.1 Create `frontend/src/renderer/components/services/ServiceCatalogBrowser.vue`
    - Search input with debounce, component dropdown filter
    - Service list with serviceName, component badge, verb badge, noun, authenticate/allowRemote indicators
    - Loading spinner, error state with retry, empty state
    - Pagination controls (compute totalPages from total/pageSize)
    - Click navigates to `/services/:serviceName`
    - Follow EntityCatalogBrowser.vue pattern and dark theme CSS
    - _Requirements: 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8, 4.9, 4.10_
  - [x] 5.2 Create `frontend/src/renderer/views/ServiceListView.vue`
    - Mount ServiceCatalogBrowser, trigger fetchServices on mount
    - _Requirements: 4.1_
  - [x]* 5.3 Write property test for service list item rendering
    - **Property 5: Service list item renders all required fields**
    - **Validates: Requirements 4.5**

- [x] 6. Create Service detail components and view
  - [x] 6.1 Create `frontend/src/renderer/components/services/ServiceDetailPanel.vue`
    - Display basic info: serviceName, path, verb, noun, component, authenticate, allowRemote
    - _Requirements: 5.2_
  - [x] 6.2 Create `frontend/src/renderer/components/services/ServiceInParamsPanel.vue`
    - Display inParams as a list
    - _Requirements: 5.4_
  - [x] 6.3 Create `frontend/src/renderer/components/services/ServiceOutParamsPanel.vue`
    - Display outParams as a list
    - _Requirements: 5.5_
  - [x] 6.4 Create `frontend/src/renderer/views/ServiceDetailView.vue`
    - Route param serviceName, trigger fetchServiceDetail on mount
    - Tab layout: 概览 (ServiceDetailPanel), 入参 (ServiceInParamsPanel), 出参 (ServiceOutParamsPanel)
    - Back navigation button, clearDetail on unmount
    - Loading/error/404 states
    - _Requirements: 5.1, 5.3, 5.6, 5.7, 5.8, 7.1, 7.3_
  - [x]* 6.5 Write property test for detail panel and params panels rendering
    - **Property 6: Service detail panel renders all basic info**
    - **Property 7: Params panels render all items**
    - **Validates: Requirements 5.2, 5.4, 5.5**

- [x] 7. Checkpoint - Components and Views
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8. Routing and navigation integration
  - [x] 8.1 Add service routes to `frontend/src/renderer/router/index.js`
    - `/services` → ServiceListView (requiresAuth)
    - `/services/:serviceName` → ServiceDetailView (requiresAuth)
    - _Requirements: 6.1, 6.2_
  - [x] 8.2 Update `frontend/src/renderer/stores/navigation.ts`
    - Add 'services' to NavId type
    - Add Service 探索 to NAV_ITEMS
    - Add services PanelContext and DEFAULT_CONTEXTS
    - Update componentMap in currentPanelComponent getter
    - _Requirements: 6.3, 6.4, 6.5_

- [x] 9. Final checkpoint - Full integration
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Follow Entity Explorer (23-00) patterns exactly for consistency
- All Vue components use `<script setup lang="ts">`
- CSS follows existing dark theme pattern with `[data-theme="dark"]`
- Key differences from Entity: `total` not `totalCount`, `q` not `keyword`, no `pageMaxIndex`
- Store file named `serviceCatalog.ts` to avoid conflict with existing `serviceInvoker.ts`
