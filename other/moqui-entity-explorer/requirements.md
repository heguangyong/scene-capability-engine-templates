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

# Requirements Document

## Introduction

{{SPEC_NAME_TITLE}} 前端模块，为 331-poc ERP 现代化项目提供 Entity 浏览和分析功能。用户可以按组件浏览 Entity 目录、搜索 Entity、查看 Entity 定义详情（字段、关系）。该模块遵循 Screen Explorer (22-00) 的架构模式，复用已有的后端 Entity Catalog REST API。

## Glossary

- **Entity_Explorer**: Entity 浏览和分析前端模块，包含列表视图和详情视图
- **Entity_Catalog_API**: 后端 REST API 端点，提供 Entity 目录、定义、关系数据（`/api/v1/entities`, `/api/v1/entities/{entityName}/definition`, `/api/v1/entities/{entityName}/relationships`）
- **Entity_Store**: Pinia Store，管理 Entity 列表、筛选、分页、详情的状态
- **Entity_Catalog_Browser**: Entity 列表浏览组件，支持搜索、组件筛选、分页
- **Entity_Detail_Panel**: Entity 详情面板，展示基本信息、字段表、关系面板
- **Entity_Summary**: Entity 摘要数据，包含 entityName、packageName、component、fieldCount、relationshipCount 等
- **Entity_Definition**: Entity 完整定义，包含字段列表和关系列表
- **Navigation_Store**: 全局导航 Pinia Store，管理侧边栏导航项和面板上下文

## Requirements

### Requirement 1: Entity Catalog API 服务层

**User Story:** As a developer, I want a typed API service that communicates with the Entity Catalog REST endpoints, so that the frontend can retrieve entity catalog data reliably.

#### Acceptance Criteria

1. THE Entity_Catalog_API_Service SHALL expose a `listEntities` method that accepts optional component, keyword, pageIndex, and pageSize parameters and returns typed EntityListData
2. THE Entity_Catalog_API_Service SHALL expose a `getEntityDefinition` method that accepts an entityName parameter and returns typed EntityDefinitionData
3. THE Entity_Catalog_API_Service SHALL expose a `listRelationships` method that accepts an entityName parameter and returns typed EntityRelationshipsData
4. THE Entity_Catalog_API_Service SHALL reuse the existing apiV1Client for all HTTP requests
5. WHEN the API returns an error response, THE Entity_Catalog_API_Service SHALL propagate the typed ApiV1Result error to the caller

### Requirement 2: Entity State Management

**User Story:** As a developer, I want a Pinia store that manages entity data, filters, pagination, and loading states, so that components can reactively display entity information.

#### Acceptance Criteria

1. THE Entity_Store SHALL maintain a list of EntitySummary items, pagination metadata, and loading/error states for the list view
2. THE Entity_Store SHALL maintain currentEntity (EntityDefinitionData), currentRelationships, and detail loading/error states for the detail view
3. THE Entity_Store SHALL provide a `fetchEntities` action that calls Entity_Catalog_API_Service.listEntities with current filter and pagination state
4. THE Entity_Store SHALL provide a `fetchEntityDetail` action that calls Entity_Catalog_API_Service.getEntityDefinition for a given entityName
5. THE Entity_Store SHALL provide a `fetchEntityRelationships` action that calls Entity_Catalog_API_Service.listRelationships for a given entityName
6. THE Entity_Store SHALL provide a `setFilter` action that updates component/keyword filters, resets pageIndex to 0, and re-fetches the entity list
7. THE Entity_Store SHALL provide a `setPage` action that updates pageIndex and re-fetches the entity list
8. WHEN a fetch action starts, THE Entity_Store SHALL set the corresponding loading flag to true and clear the previous error
9. WHEN a fetch action completes successfully, THE Entity_Store SHALL update the corresponding data and set loading to false
10. IF a fetch action fails, THEN THE Entity_Store SHALL set the corresponding error message and set loading to false

### Requirement 3: Entity Type Definitions

**User Story:** As a developer, I want TypeScript type definitions that match the backend Entity Catalog API response shapes, so that the frontend has compile-time type safety.

#### Acceptance Criteria

1. THE Entity_Explorer type module SHALL define EntitySummary with fields: entityName, entityNameShort, packageName, shortAlias, groupName, isView, tableName, fieldCount, relationshipCount, component
2. THE Entity_Explorer type module SHALL define EntityListData with fields: entities (EntitySummary[]), totalCount, pageIndex, pageSize, pageMaxIndex, generatedAt, component (optional), filters (optional)
3. THE Entity_Explorer type module SHALL define EntityListParams with optional fields: component, packagePrefix, keyword, pageIndex, pageSize
4. THE Entity_Explorer type module SHALL define EntityDefinitionData with fields: entityName, entityNameShort, packageName, shortAlias, groupName, isView, isDynamicView, tableName, fullTableName, pkFields, component, fieldCount, relationshipCount, fields (EntityField[]), relationships (EntityRelationship[])
5. THE Entity_Explorer type module SHALL define EntityField with fields: name, type, javaType, isPk, columnName, encrypt, createOnly, default
6. THE Entity_Explorer type module SHALL define EntityRelationship with fields: name, shortAlias, type, isTypeOne, relatedEntity, title, dependent, keyMap
7. THE Entity_Explorer type module SHALL define EntityRelationshipsData with fields: entityName, relationshipCount, relationships (EntityRelationship[])
8. THE Entity_Explorer type module SHALL define EntityPaginationMeta with fields: totalCount, pageIndex, pageSize, pageMaxIndex

### Requirement 4: Entity List View

**User Story:** As a user, I want to browse and search the entity catalog with filtering and pagination, so that I can find entities of interest.

#### Acceptance Criteria

1. WHEN the Entity list view loads, THE Entity_Explorer SHALL call Entity_Store.fetchEntities to populate the entity list
2. THE Entity_Catalog_Browser component SHALL display a search input for keyword filtering and a dropdown for component filtering
3. WHEN a user types in the search input, THE Entity_Catalog_Browser SHALL debounce the input and call Entity_Store.setFilter with the keyword
4. WHEN a user selects a component from the dropdown, THE Entity_Catalog_Browser SHALL call Entity_Store.setFilter with the selected component
5. THE Entity_Catalog_Browser SHALL display each EntitySummary as a list item showing entityName, component badge, packageName, and fieldCount/relationshipCount
6. WHEN a user clicks an entity list item, THE Entity_Explorer SHALL navigate to the entity detail route `/entities/:entityName`
7. WHILE Entity_Store.listLoading is true, THE Entity_Catalog_Browser SHALL display a loading spinner
8. IF Entity_Store.listError is set, THEN THE Entity_Catalog_Browser SHALL display the error message with a retry button
9. WHEN no entities match the current filters, THE Entity_Catalog_Browser SHALL display an empty state message
10. THE Entity_Catalog_Browser SHALL display pagination controls and call Entity_Store.setPage when the user changes pages

### Requirement 5: Entity Detail View

**User Story:** As a user, I want to view the full definition of an entity including its fields and relationships, so that I can understand the entity structure.

#### Acceptance Criteria

1. WHEN the Entity detail view loads with a route parameter entityName, THE Entity_Explorer SHALL call Entity_Store.fetchEntityDetail and Entity_Store.fetchEntityRelationships
2. THE Entity_Detail_Panel SHALL display entity basic information: entityName, packageName, component, groupName, tableName, isView, pkFields
3. THE Entity_Detail_Panel SHALL provide tab navigation with tabs: 概览 (overview), 字段 (fields), 关系 (relationships)
4. WHEN the 字段 tab is active, THE Entity_Fields_Table component SHALL display all entity fields in a table with columns: name, type, javaType, isPk, columnName
5. WHEN the 关系 tab is active, THE Entity_Relationships_Panel component SHALL display all entity relationships showing: name, type, relatedEntity, keyMap
6. WHILE Entity_Store.detailLoading is true, THE Entity_Detail_Panel SHALL display a loading spinner
7. IF Entity_Store.detailError is set, THEN THE Entity_Detail_Panel SHALL display the error message with a retry button
8. THE Entity detail view SHALL provide a back navigation button that returns to the entity list view

### Requirement 6: Routing and Navigation Integration

**User Story:** As a user, I want to access the Entity Explorer from the sidebar navigation and navigate between list and detail views, so that the module is integrated into the application.

#### Acceptance Criteria

1. THE Router SHALL register route `/entities` mapped to EntityListView with meta `requiresAuth: true`
2. THE Router SHALL register route `/entities/:entityName` mapped to EntityDetailView with meta `requiresAuth: true`
3. THE Navigation_Store SHALL include an 'entities' NavId with route '/entities', label 'Entity 探索', and description '浏览和分析 Moqui Entity 定义'
4. THE Navigation_Store SHALL include an entities PanelContext with currentKeyword and currentComponent fields
5. WHEN a user clicks the Entity 探索 navigation item, THE Entity_Explorer SHALL navigate to the `/entities` route

### Requirement 7: Error Handling and Edge Cases

**User Story:** As a user, I want graceful error handling when API calls fail or data is unavailable, so that the application remains usable.

#### Acceptance Criteria

1. IF the Entity_Catalog_API returns a 404 for an entity definition, THEN THE Entity_Detail_Panel SHALL display a "Entity 未找到" message
2. IF a network error occurs during any Entity API call, THEN THE Entity_Store SHALL set a user-friendly error message
3. WHEN navigating away from the entity detail view, THE Entity_Store SHALL clear the detail state via clearDetail action
