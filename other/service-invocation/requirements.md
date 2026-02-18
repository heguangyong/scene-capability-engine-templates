---
name: service-invocation
category: other
description: Template for Service Invocation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

{{SPEC_NAME_TITLE}} 前端模块，为 331-poc ERP 现代化项目的 Service Explorer 添加服务调用能力。用户可以从 Service 详情页进入"调用"标签页，填写入参表单并同步或异步调用 Moqui 服务，查看调用结果。表单根据 Service 入参定义自动生成，支持多种字段类型、必填验证、默认值预填、JSON 编辑器（复杂参数）、异步任务轮询等特性。该模块扩展已有的 Service Explorer (Spec 24-00)，复用已实现的 `serviceInvoker.ts` API 客户端和后端 {{SPEC_NAME_TITLE}} REST API。

## Glossary

- **Service_Invoke_Module**: Service 调用前端模块，包含参数表单、结果面板、调用 Tab 等组件
- **Service_Invoke_Store**: Pinia Store，管理服务调用状态：参数表单数据、加载状态、调用结果、错误信息、调用历史
- **Service_Param_Form**: 服务参数表单组件，根据 Service 入参元数据自动生成输入控件
- **Service_Result_Panel**: 服务调用结果面板组件，展示调用返回的输出参数、事务元数据、错误信息
- **Service_Invoke_Tab**: 调用 Tab 容器组件，协调 Service_Param_Form 和 Service_Result_Panel
- **Service_Invoker**: 已有的前端 API 客户端（serviceInvoker.ts），提供 `invoke()`、`invokeAsync()`、`getJobStatus()` 方法
- **Service_Catalog_Store**: 已有的 Pinia Store（serviceCatalog.ts），管理 Service 浏览和详情数据
- **Sync_Invocation**: 同步调用模式，请求阻塞直到服务执行完成并返回结果
- **Async_Invocation**: 异步调用模式，请求立即返回 jobId，客户端通过轮询获取最终结果
- **Job_Polling**: 异步任务轮询机制，定期查询 jobId 状态直到任务完成或失败
- **Invocation_History**: 调用历史记录，在内存中保存最近 N 次调用结果供用户回顾

## Requirements

### Requirement 1: Service Invoke State Management

**User Story:** As a developer, I want a dedicated Pinia store for service invocation, so that components can reactively manage invocation parameters, results, and async job states.

#### Acceptance Criteria

1. THE Service_Invoke_Store SHALL maintain the current service name, invocation mode (sync/async), parameter form data, loading state, invocation result, and error state
2. THE Service_Invoke_Store SHALL provide an `invokeSyncAction` that calls Service_Invoker.invoke with the current service name and form parameters, and stores the result
3. THE Service_Invoke_Store SHALL provide an `invokeAsyncAction` that calls Service_Invoker.invokeAsync with the current service name and form parameters, stores the jobId, and starts polling
4. THE Service_Invoke_Store SHALL provide a `pollJobStatus` action that calls Service_Invoker.getJobStatus with the current jobId at a configurable interval (default 2 seconds) until the job completes or fails
5. WHEN a sync invocation starts, THE Service_Invoke_Store SHALL set loading to true and clear the previous result and error
6. WHEN a sync invocation completes successfully, THE Service_Invoke_Store SHALL set loading to false, store the result data, and add the invocation to history
7. IF a sync invocation fails, THEN THE Service_Invoke_Store SHALL set loading to false and store the error details including field-level validation errors
8. WHEN an async invocation starts, THE Service_Invoke_Store SHALL set loading to true, clear the previous result and error, and set the polling state to active
9. WHEN async job polling detects a completed job, THE Service_Invoke_Store SHALL stop polling, set loading to false, store the final result, and add the invocation to history
10. IF async job polling detects a failed job, THEN THE Service_Invoke_Store SHALL stop polling, set loading to false, and store the error details
11. THE Service_Invoke_Store SHALL maintain an invocation history array of the last 10 invocation results in memory
12. THE Service_Invoke_Store SHALL provide a `clearResult` action that resets the current result and error state
13. THE Service_Invoke_Store SHALL provide a `resetAll` action that clears all state including form data, result, error, and stops any active polling

### Requirement 2: Service Parameter Form

**User Story:** As a user, I want an auto-generated parameter form based on service input parameters, so that I can fill in invocation parameters with proper field types and validation.

#### Acceptance Criteria

1. THE Service_Param_Form SHALL generate form fields based on the service's inParams metadata (name, type, required, defaultValue)
2. THE Service_Param_Form SHALL map parameter types to appropriate input controls: String→text, Integer/Long→number (step=1), Float/Double/BigDecimal→number (step=any), Boolean→checkbox, Date→date, Timestamp→datetime-local, Time→time, default→text
3. WHEN a parameter has a defaultValue defined, THE Service_Param_Form SHALL pre-populate the corresponding field with that default value
4. THE Service_Param_Form SHALL mark required parameters with a visual indicator and validate that required fields are non-empty before submission
5. WHEN a parameter type is Map, List, or unrecognized complex type, THE Service_Param_Form SHALL render a textarea with JSON editing capability
6. THE Service_Param_Form SHALL provide a "清空" (Clear) button that resets all fields to their default values
7. IF the backend returns validation errors with field-level details, THEN THE Service_Param_Form SHALL display each error message adjacent to the corresponding parameter field

### Requirement 3: Service Result Panel

**User Story:** As a user, I want to see invocation results in a structured format, so that I can understand the service response and diagnose issues.

#### Acceptance Criteria

1. WHEN a sync invocation succeeds, THE Service_Result_Panel SHALL display the output data in a formatted JSON tree view
2. WHEN a sync invocation fails, THE Service_Result_Panel SHALL display the error code, error message, and field-level validation details
3. WHEN the result contains transaction metadata (_tx), THE Service_Result_Panel SHALL display the transaction information in a collapsible section
4. THE Service_Result_Panel SHALL provide a "复制" (Copy) button that copies the full result JSON to the clipboard
5. WHILE an async job is polling, THE Service_Result_Panel SHALL display the current job status with a polling indicator animation
6. WHEN an async job completes, THE Service_Result_Panel SHALL display the final result in the same format as sync results

### Requirement 4: Service Invoke Tab Integration

**User Story:** As a user, I want a "调用" (Invoke) tab in the Service detail view, so that I can invoke services alongside viewing their metadata.

#### Acceptance Criteria

1. THE ServiceDetailView SHALL add a "调用" tab button after the existing 概览/入参/出参 tabs
2. WHEN the 调用 tab is active, THE ServiceDetailView SHALL render the Service_Invoke_Tab component
3. THE Service_Invoke_Tab SHALL display the Service_Param_Form on the left/top and the Service_Result_Panel on the right/bottom
4. THE Service_Invoke_Tab SHALL provide a "同步调用" (Sync Invoke) button and an "异步调用" (Async Invoke) button
5. WHILE an invocation is in progress, THE Service_Invoke_Tab SHALL disable both invoke buttons to prevent duplicate submissions
6. WHEN an invocation completes, THE Service_Invoke_Tab SHALL display a brief success or error notification
7. WHEN switching away from the 调用 tab, THE Service_Invoke_Store SHALL retain its state so returning preserves the user's form data and last result

### Requirement 5: Invocation History

**User Story:** As a user, I want to see my recent invocation history, so that I can compare results and re-invoke with previous parameters.

#### Acceptance Criteria

1. THE Service_Invoke_Tab SHALL display a history section showing the last 10 invocations for the current service
2. WHEN a user clicks a history entry, THE Service_Invoke_Tab SHALL populate the parameter form with that entry's parameters and display the stored result
3. THE Service_Invoke_Store SHALL store each history entry with: timestamp, parameters, result (success/error), and invocation mode (sync/async)

### Requirement 6: Error Handling and Loading States

**User Story:** As a user, I want clear feedback during service invocations, so that I understand the current state and any issues.

#### Acceptance Criteria

1. WHEN a service invocation succeeds, THE Service_Invoke_Module SHALL display a brief success notification "调用成功"
2. IF a network error occurs during invocation, THEN THE Service_Invoke_Module SHALL display "网络错误，请重试"
3. IF the backend returns a 400 validation error, THEN THE Service_Invoke_Module SHALL display the specific validation error details with field-level messages
4. IF the backend returns a 500 server error, THEN THE Service_Invoke_Module SHALL display the error message from the response
5. WHILE any invocation or polling operation is in progress, THE Service_Invoke_Module SHALL prevent duplicate submissions by disabling invoke buttons
6. IF async job polling exceeds a maximum timeout (60 seconds), THEN THE Service_Invoke_Store SHALL stop polling and display a timeout error message "异步任务超时，请稍后查询"
