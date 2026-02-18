---
name: entity-crud
category: other
description: Template for Entity Crud
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: Entity CRUD

## Overview

为 Entity Explorer 添加数据 CRUD 功能。扩展 entityService、新建 entityCrud store、新建 4 个 Vue 组件（EntityDataTab/EntityDataTable/EntityRecordForm/EntityDeleteDialog），修改 EntityDetailView 添加"数据"tab。

## Tasks

- [x] 1. Field type mapping 工具函数和 Entity Service 扩展
  - [x] 1.1 Create `frontend/src/renderer/utils/fieldTypeMapping.ts`
    - Implement `mapFieldToInput(fieldType)` — maps Moqui field types to HTML input configs
    - Implement `buildCompositeId(record, pkFields)` — joins PK values with commas
    - Implement `parseCompositeId(compositeId, pkFields)` — splits composite ID back to field map
    - Export `FieldInputConfig` interface
    - _Requirements: 5.2, 6.1_

  - [x]* 1.2 Write property tests for fieldTypeMapping
    - **Property 9: Field type mapping correctness**
    - **Property 12: Composite ID round-trip**
    - **Validates: Requirements 5.2, 6.1, 6.4**

  - [x] 1.3 Extend `frontend/src/renderer/services/entityService.ts`
    - Add `batch(entityName, body)` method — POST to `/entities/{entityName}/batch`
    - Add `listRelated(entityName, entityId, relationshipName, params)` method — GET to `/entities/{entityName}/{entityId}/related/{relationshipName}`
    - Both methods must use `encodeURIComponent` for all path segments
    - _Requirements: 1.1, 1.2, 1.3_

  - [x]* 1.4 Write property tests for entityService extensions
    - **Property 1: Service URL construction for new methods**
    - **Property 2: Validation error detail preservation**
    - **Validates: Requirements 1.1, 1.2, 1.3, 1.4, 8.4**

- [x] 2. Entity CRUD Store
  - [x] 2.1 Create `frontend/src/renderer/stores/entityCrud.ts`
    - Define `EntityCrudState` interface with records, pagination, loading/error, filters/sort/page, mutation states
    - Implement getters: `totalPages`, `hasNextPage`, `hasPreviousPage`, `isReadOnly`
    - Implement `initialize(entityName, definition)` — sets context, triggers fetchRecords
    - Implement `fetchRecords()` — calls entityService.list with current state params
    - Implement `createRecord(data)` — calls entityService.create, refreshes list on success, sets successMessage
    - Implement `updateRecord(entityId, data)` — calls entityService.update, refreshes list on success
    - Implement `deleteRecord(entityId)` — calls entityService.delete, refreshes list on success
    - Implement `setPage(pageIndex)`, `setSort(orderBy)`, `setFilters(filters)` — update state and re-fetch
    - `setSort` and `setFilters` must reset pageIndex to 0
    - Handle loading/error lifecycle: loading=true on start, clear error; loading=false on complete
    - Handle network errors with "网络错误，请重试" message
    - Handle 404 with "记录未找到" message and list refresh
    - Preserve field-level validation errors in `fieldErrors` state
    - Implement `clearMutationState()` and `reset()`
    - _Requirements: 2.1–2.12, 8.1–8.5_

  - [x]* 2.2 Write property tests for entityCrud store
    - **Property 3: fetchRecords passes current store state**
    - **Property 4: Successful mutation refreshes record list**
    - **Property 5: Action state lifecycle transitions**
    - **Property 6: Re-query actions reset pageIndex to zero**
    - **Validates: Requirements 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9, 2.11, 2.12, 8.2**

- [x] 3. Checkpoint - Core logic tests
  - Ensure all tests pass, ask the user if questions arise.

- [x] 4. EntityDeleteDialog and EntityRecordForm components
  - [x] 4.1 Create `frontend/src/renderer/components/entities/EntityDeleteDialog.vue`
    - Props: `visible` (boolean), `record` (Record), `pkFields` (string[]), `deleting` (boolean), `error` (string|null)
    - Emits: `confirm`, `cancel`
    - Display PK field values in confirmation message
    - Disable confirm button while deleting
    - Show error message if delete fails
    - Dark theme support with `[data-theme="dark"]`
    - _Requirements: 7.1–7.5_

  - [x] 4.2 Create `frontend/src/renderer/components/entities/EntityRecordForm.vue`
    - Props: `visible` (boolean), `mode` ('create'|'edit'), `fields` (EntityField[]), `pkFields` (string[]), `record` (Record|null), `submitting` (boolean), `fieldErrors` (FieldError[]), `error` (string|null)
    - Emits: `submit(data)`, `cancel`
    - Generate form inputs from fields array using `mapFieldToInput`
    - Create mode: PK fields required + editable, pre-populate defaults
    - Edit mode: PK fields read-only, pre-populate with record values
    - Display field-level errors next to corresponding inputs
    - Disable submit button while submitting
    - Dark theme support
    - _Requirements: 5.1–5.11_

  - [x]* 4.3 Write property tests for EntityRecordForm
    - **Property 10: PK field editability by form mode**
    - **Property 11: Form field generation with defaults**
    - **Property 13: Field-level validation error display**
    - **Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5, 5.8**

- [x] 5. EntityDataTable and EntityDataTab components
  - [x] 5.1 Create `frontend/src/renderer/components/entities/EntityDataTable.vue`
    - Props: `records` (Record[]), `fields` (EntityField[]), `pkFields` (string[]), `pagination` (EntityPaginationMeta|null), `loading` (boolean), `error` (string|null), `readOnly` (boolean), `currentOrderBy` (string|null)
    - Emits: `create`, `edit(record)`, `delete(record)`, `page-change(pageIndex)`, `sort-change(orderBy)`, `filter-change(filters)`
    - Render table columns from fields array, highlight PK columns
    - Column header click toggles sort (asc/desc/none)
    - Filter input row for column-level filtering
    - Pagination controls: prev/next buttons, page info display
    - Toolbar with "新建" button (hidden when readOnly)
    - Row action buttons: "编辑", "删除" (hidden when readOnly)
    - Loading overlay, error state with retry, empty state "暂无数据"
    - Dark theme support
    - _Requirements: 4.1–4.13_

  - [x]* 5.2 Write property tests for EntityDataTable
    - **Property 7: View entity enforces read-only mode**
    - **Property 8: Table columns match entity fields with PK highlight**
    - **Validates: Requirements 3.4, 4.1, 4.2, 6.2**

  - [x] 5.3 Create `frontend/src/renderer/components/entities/EntityDataTab.vue`
    - Props: `entityName` (string), `entityDefinition` (EntityDefinitionData|null)
    - Orchestrates EntityDataTable, EntityRecordForm, EntityDeleteDialog
    - On mount: initialize entityCrud store with entityName + definition, fetch first page
    - Manage form/dialog visibility state (showForm, formMode, showDeleteDialog, selectedRecord)
    - Wire events: create → open form in create mode, edit → open form in edit mode, delete → open dialog
    - On form submit: call store.createRecord or store.updateRecord, close on success
    - On delete confirm: call store.deleteRecord, close on success
    - Build composite ID using `buildCompositeId` for edit/delete operations
    - Pass store state (records, pagination, loading, error, mutating, fieldErrors, successMessage) to child components
    - _Requirements: 3.1–3.5, 6.1–6.4_

- [x] 6. EntityDetailView integration
  - [x] 6.1 Modify `frontend/src/renderer/views/EntityDetailView.vue`
    - Extend activeTab type to include 'data'
    - Add "数据" tab button in tab-bar
    - Import and render EntityDataTab when activeTab === 'data'
    - Pass entityName and entityDefinition props to EntityDataTab
    - _Requirements: 3.1, 3.2_

- [x] 7. Final checkpoint - Full integration
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties
- Unit tests validate specific examples and edge cases
- All new components follow existing dark theme pattern with `[data-theme="dark"]`
- All new components use `<script setup lang="ts">`
