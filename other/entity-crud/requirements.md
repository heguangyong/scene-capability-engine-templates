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

# Requirements Document

## Introduction

Entity CRUD 前端模块，为 331-poc ERP 现代化项目的 Entity Explorer 添加数据操作能力。用户可以从 Entity 详情页进入"数据"标签页，浏览实体记录、创建新记录、编辑和删除现有记录。表单根据 Entity 字段定义自动生成，支持分页、排序、筛选、复合主键等特性。该模块扩展已有的 Entity Explorer (Spec 23-00)，复用后端 Entity CRUD REST API。

## Glossary

- **Entity_CRUD_Module**: Entity 数据操作前端模块，包含数据表格、表单、删除确认等组件
- **Entity_Data_Table**: 实体记录数据表格组件，支持分页、排序、筛选，展示实体记录列表
- **Entity_Record_Form**: 实体记录表单组件，根据 EntityField 定义自动生成输入控件，用于创建和编辑记录
- **Entity_CRUD_Store**: Pinia Store，管理实体记录的列表、分页、CRUD 操作状态
- **Entity_Service**: 前端 API 服务层（entityService.ts），封装后端 Entity CRUD REST API 调用
- **Entity_Definition**: 实体定义数据（EntityDefinitionData），包含字段列表、主键字段、字段类型等元数据
- **Entity_Field**: 实体字段定义（EntityField），包含 name、type、javaType、isPk、createOnly、default 等属性
- **Composite_PK**: 复合主键，由多个字段组成，在 API 中以逗号分隔传递
- **Field_Type_Mapping**: 字段类型到表单输入控件的映射规则

## Requirements

### Requirement 1: Entity Service 扩展

**User Story:** As a developer, I want the entityService to support batch operations and related entity queries, so that the CRUD module can leverage the full backend API.

#### Acceptance Criteria

1. THE Entity_Service SHALL expose a `batch` method that accepts an entityName and a batch request body containing operations array, and returns the batch result
2. THE Entity_Service SHALL expose a `listRelated` method that accepts entityName, entityId, relationshipName, and optional pagination/filter parameters, and returns a paginated list of related records
3. THE Entity_Service SHALL encode entityName and entityId using `encodeURIComponent` in all URL path segments
4. WHEN the backend returns a validation error for a CRUD operation, THE Entity_Service SHALL preserve the error details including field-level validation messages

### Requirement 2: Entity CRUD State Management

**User Story:** As a developer, I want a dedicated Pinia store for entity record CRUD operations, so that components can reactively manage record data, pagination, and operation states.

#### Acceptance Criteria

1. THE Entity_CRUD_Store SHALL maintain a list of entity records, pagination metadata (totalCount, pageIndex, pageSize, pageMaxIndex), and loading/error states for the record list
2. THE Entity_CRUD_Store SHALL maintain the current entityName context and the associated EntityDefinitionData for form generation
3. THE Entity_CRUD_Store SHALL provide a `fetchRecords` action that calls Entity_Service.list with current entityName, filters, sorting, and pagination parameters
4. THE Entity_CRUD_Store SHALL provide a `createRecord` action that calls Entity_Service.create and refreshes the record list on success
5. THE Entity_CRUD_Store SHALL provide an `updateRecord` action that calls Entity_Service.update and refreshes the record list on success
6. THE Entity_CRUD_Store SHALL provide a `deleteRecord` action that calls Entity_Service.delete and refreshes the record list on success
7. WHEN a fetch or mutation action starts, THE Entity_CRUD_Store SHALL set the corresponding loading flag to true and clear the previous error
8. WHEN a mutation action completes successfully, THE Entity_CRUD_Store SHALL set a success message for user feedback
9. IF a CRUD action fails, THEN THE Entity_CRUD_Store SHALL set the corresponding error message including any field-level validation details
10. THE Entity_CRUD_Store SHALL provide a `setPage` action that updates pageIndex and re-fetches records
11. THE Entity_CRUD_Store SHALL provide a `setSort` action that updates the orderBy parameter and re-fetches records from page 0
12. THE Entity_CRUD_Store SHALL provide a `setFilters` action that updates filter parameters and re-fetches records from page 0

### Requirement 3: Entity Data Tab Integration

**User Story:** As a user, I want a "数据" (Data) tab in the Entity detail view, so that I can browse entity records alongside the existing metadata tabs.

#### Acceptance Criteria

1. THE EntityDetailView SHALL add a "数据" tab button after the existing 概览/字段/关系 tabs
2. WHEN the 数据 tab is active, THE EntityDetailView SHALL render the Entity_Data_Table component with the current entity's records
3. WHEN the 数据 tab is activated, THE Entity_CRUD_Store SHALL initialize with the current entityName and EntityDefinitionData, then fetch the first page of records
4. WHILE the current entity is a view entity (isView=true), THE 数据 tab SHALL display records in read-only mode without create/edit/delete controls
5. WHEN switching away from the 数据 tab, THE Entity_CRUD_Store SHALL retain its state so returning to the tab preserves the user's position

### Requirement 4: Entity Data Table

**User Story:** As a user, I want to view entity records in a paginated data table with sorting and filtering, so that I can browse and find specific records.

#### Acceptance Criteria

1. THE Entity_Data_Table SHALL display entity records in a table with columns derived from the EntityDefinitionData fields array
2. THE Entity_Data_Table SHALL highlight primary key columns with a visual indicator
3. WHEN a user clicks a column header, THE Entity_Data_Table SHALL toggle sorting (ascending/descending/none) on that column and re-fetch records
4. THE Entity_Data_Table SHALL display pagination controls showing current page, total pages, and total record count
5. WHEN a user changes the page, THE Entity_Data_Table SHALL call Entity_CRUD_Store.setPage to load the target page
6. THE Entity_Data_Table SHALL provide a filter input row where users can enter filter values for individual columns
7. WHEN a user enters a filter value and confirms, THE Entity_Data_Table SHALL call Entity_CRUD_Store.setFilters to apply the filter and re-fetch records
8. WHILE Entity_CRUD_Store.listLoading is true, THE Entity_Data_Table SHALL display a loading overlay
9. IF Entity_CRUD_Store.listError is set, THEN THE Entity_Data_Table SHALL display the error message with a retry button
10. WHEN no records match the current filters, THE Entity_Data_Table SHALL display an empty state message "暂无数据"
11. THE Entity_Data_Table SHALL provide a "新建" (Create) button in the toolbar that opens the Entity_Record_Form in create mode
12. THE Entity_Data_Table SHALL provide an "编辑" (Edit) action button on each row that opens the Entity_Record_Form in edit mode with the record data
13. THE Entity_Data_Table SHALL provide a "删除" (Delete) action button on each row that triggers a confirmation dialog

### Requirement 5: Entity Record Form

**User Story:** As a user, I want an auto-generated form for creating and editing entity records, so that I can manipulate data with proper field types and validation.

#### Acceptance Criteria

1. THE Entity_Record_Form SHALL generate form fields based on the EntityDefinitionData fields array
2. THE Entity_Record_Form SHALL map entity field types to appropriate HTML input types: id/text-short/text-medium/text-long→text, number-integer→number, number-decimal/number-float/currency-amount→number with step, date-time→datetime-local, date→date, time→time, text-indicator→checkbox, text-very-long→textarea
3. WHEN in create mode, THE Entity_Record_Form SHALL mark primary key fields as required and editable
4. WHEN in edit mode, THE Entity_Record_Form SHALL display primary key fields as read-only
5. THE Entity_Record_Form SHALL pre-populate fields that have a default value defined in the EntityField definition when in create mode
6. WHEN a user submits the form in create mode, THE Entity_Record_Form SHALL call Entity_CRUD_Store.createRecord with the form data
7. WHEN a user submits the form in edit mode, THE Entity_Record_Form SHALL call Entity_CRUD_Store.updateRecord with the entity ID and changed fields
8. IF the backend returns validation errors, THEN THE Entity_Record_Form SHALL display field-level error messages next to the corresponding input fields
9. WHILE a form submission is in progress, THE Entity_Record_Form SHALL disable the submit button and show a loading indicator
10. WHEN a form submission succeeds, THE Entity_Record_Form SHALL close the form and display a success notification
11. THE Entity_Record_Form SHALL provide a "取消" (Cancel) button that closes the form without saving

### Requirement 6: Composite Primary Key Handling

**User Story:** As a user, I want to work with entities that have composite primary keys, so that I can correctly identify and manipulate multi-key records.

#### Acceptance Criteria

1. THE Entity_CRUD_Module SHALL construct entity IDs for composite primary keys by joining PK field values with commas in the order defined by pkFields
2. WHEN displaying a record with composite PK in the data table, THE Entity_Data_Table SHALL show all PK field values
3. WHEN editing a record with composite PK, THE Entity_Record_Form SHALL display all PK fields as read-only
4. WHEN deleting a record with composite PK, THE Entity_CRUD_Module SHALL pass the comma-separated composite ID to the delete API

### Requirement 7: Delete Confirmation

**User Story:** As a user, I want a confirmation dialog before deleting records, so that I can avoid accidental data loss.

#### Acceptance Criteria

1. WHEN a user clicks the delete button on a record, THE Entity_CRUD_Module SHALL display a confirmation dialog showing the record's primary key values
2. WHEN the user confirms deletion, THE Entity_CRUD_Module SHALL call Entity_CRUD_Store.deleteRecord and close the dialog
3. WHEN the user cancels deletion, THE Entity_CRUD_Module SHALL close the dialog without performing any action
4. WHILE a delete operation is in progress, THE confirmation dialog SHALL disable the confirm button and show a loading indicator
5. IF the delete operation fails, THEN THE Entity_CRUD_Module SHALL display the error message in the confirmation dialog

### Requirement 8: Error Handling and Loading States

**User Story:** As a user, I want clear feedback during data operations, so that I understand the current state and any issues.

#### Acceptance Criteria

1. WHEN a CRUD operation succeeds, THE Entity_CRUD_Module SHALL display a brief success notification (e.g., "创建成功", "更新成功", "删除成功")
2. IF a network error occurs during any CRUD operation, THEN THE Entity_CRUD_Module SHALL display a user-friendly error message "网络错误，请重试"
3. IF the backend returns a 404 for a record operation, THEN THE Entity_CRUD_Module SHALL display "记录未找到" and refresh the record list
4. IF the backend returns a 400 validation error, THEN THE Entity_CRUD_Module SHALL display the specific validation error details
5. WHILE any async operation is in progress, THE Entity_CRUD_Module SHALL prevent duplicate submissions by disabling action buttons
