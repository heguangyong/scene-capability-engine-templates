---
name: moqui-entity-explorer
category: other
description: Template for Moqui Entity Explorer
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: {{SPEC_NAME_TITLE}}

## Overview

按 Screen Explorer 模式实现 Entity Explorer 前端模块：types → service → store → components → views → routes → navigation。每层增量构建，逐步集成。

## Tasks

- [x] 1. Create Entity type definitions
  - [x] 1.1 Create `frontend/src/renderer/types/entity.ts` with all interfaces: EntitySummary, EntityListParams, EntityPaginationMeta, EntityListData, EntityField, EntityRelationship, EntityDefinitionData, EntityRelationshipsData
    - Match backend EntityCatalogService response shapes exactly
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6, 3.7, 3.8_

- [x] 2. Create Entity Catalog API service
  - [x] 2.1 Create `frontend/src/renderer/services/entityCatalogApiService.ts` with EntityCatalogApiService class
    - Implement listEntities, getEntityDefinition, listRelationships methods
    - Reuse apiV1Client, follow screenApiService.ts pattern
    - _Requirements: 1.1, 1.2, 1.3, 1.4_
  - [x]* 2.2 Write property test for API error propagation
    - **Property 1: API error propagation**
    - **Validates: Requirements 1.5**

- [x] 3. Create Entity Pinia Store
  - [x] 3.1 Create `frontend/src/renderer/stores/entity.ts` with useEntityStore
    - State: entities, pagination, listLoading, listError, currentComponent, currentKeyword, currentPageIndex, currentPageSize, currentEntity, currentRelationships, detailLoading, detailError
    - Getters: hasEntities, hasDetail, totalPages, hasNextPage, hasPreviousPage
    - Actions: fetchEntities, fetchEntityDetail, fetchEntityRelationships, setFilter, setPage, clearDetail
    - Follow screen.ts store pattern exactly
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9, 2.10_
  - [x]* 3.2 Write property test for fetchEntities passes current state
    - **Property 2: fetchEntities passes current filter/pagination state**
    - **Validates: Requirements 2.3**
  - [x]* 3.3 Write property test for setFilter resets pageIndex
    - **Property 3: setFilter always resets pageIndex to 0**
    - **Validates: Requirements 2.6**
  - [x]* 3.4 Write property test for fetch action lifecycle
    - **Property 4: Fetch action lifecycle state transitions**
    - **Validates: Requirements 2.8, 2.9, 2.10, 7.2**

- [x] 4. Checkpoint - Types, Service, Store
  - Ensure all tests pass, ask the user if questions arise.

- [x] 5. Create Entity list components and view
  - [x] 5.1 Create `frontend/src/renderer/components/entities/EntityCatalogBrowser.vue`
    - Search input with debounce, component dropdown filter
    - Entity list with entityName, component badge, packageName, fieldCount, relationshipCount
    - Loading spinner, error state with retry, empty state
    - Pagination controls
    - Click navigates to `/entities/:entityName`
    - Follow ScreenCatalogBrowser.vue pattern and dark theme CSS
    - _Requirements: 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8, 4.9, 4.10_
  - [x] 5.2 Create `frontend/src/renderer/views/EntityListView.vue`
    - Mount EntityCatalogBrowser, trigger fetchEntities on mount
    - _Requirements: 4.1_
  - [x]* 5.3 Write property test for entity list item rendering
    - **Property 5: Entity list item renders all required fields**
    - **Validates: Requirements 4.5**

- [x] 6. Create Entity detail components and view
  - [x] 6.1 Create `frontend/src/renderer/components/entities/EntityDetailPanel.vue`
    - Display basic info: entityName, packageName, component, groupName, tableName, isView, pkFields
    - Tab navigation: 概览/字段/关系
    - Loading spinner, error state with retry
    - _Requirements: 5.2, 5.3, 5.6, 5.7_
  - [x] 6.2 Create `frontend/src/renderer/components/entities/EntityFieldsTable.vue`
    - Table with columns: name, type, javaType, isPk, columnName
    - _Requirements: 5.4_
  - [x] 6.3 Create `frontend/src/renderer/components/entities/EntityRelationshipsPanel.vue`
    - Display relationships: name, type, relatedEntity, keyMap
    - _Requirements: 5.5_
  - [x] 6.4 Create `frontend/src/renderer/views/EntityDetailView.vue`
    - Route param entityName, trigger fetchEntityDetail + fetchEntityRelationships on mount
    - Back navigation button, clearDetail on unmount
    - Tab layout hosting EntityDetailPanel, EntityFieldsTable, EntityRelationshipsPanel
    - _Requirements: 5.1, 5.8, 7.3_
  - [x]* 6.5 Write property test for detail panel and sub-components rendering
    - **Property 6: Entity detail panel renders all basic info**
    - **Property 7: Detail sub-components render all items**
    - **Validates: Requirements 5.2, 5.4, 5.5**

- [x] 7. Checkpoint - Components and Views
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8. Routing and navigation integration
  - [x] 8.1 Add entity routes to `frontend/src/renderer/router/index.js`
    - `/entities` → EntityListView (requiresAuth)
    - `/entities/:entityName` → EntityDetailView (requiresAuth)
    - _Requirements: 6.1, 6.2_
  - [x] 8.2 Update `frontend/src/renderer/stores/navigation.ts`
    - Add 'entities' to NavId type
    - Add Entity 探索 to NAV_ITEMS
    - Add entities PanelContext and DEFAULT_CONTEXTS
    - Update componentMap in currentPanelComponent getter
    - _Requirements: 6.3, 6.4, 6.5_
  - [x]* 8.3 Write unit tests for route and navigation configuration
    - Verify routes registered with correct meta
    - Verify NAV_ITEMS includes entities entry
    - _Requirements: 6.1, 6.2, 6.3_

- [x] 9. Error handling for entity not found
  - [x] 9.1 Ensure EntityDetailView handles 404 response
    - Display "Entity 未找到" message when detailError indicates not found
    - _Requirements: 7.1_

- [x] 10. Final checkpoint - Full integration
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Follow Screen Explorer (22-00) patterns exactly for consistency
- All Vue components use `<script setup lang="ts">`
- CSS follows existing dark theme pattern
- Property tests use fast-check with minimum 100 iterations
