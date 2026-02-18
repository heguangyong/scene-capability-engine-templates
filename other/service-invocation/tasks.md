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

# Implementation Plan: {{SPEC_NAME_TITLE}}

## Overview

为 Service Explorer 添加服务调用功能。新建 serviceParamMapping 工具函数、serviceInvoke Pinia Store、3 个 Vue 组件（ServiceParamForm/ServiceResultPanel/ServiceInvokeTab），扩展 service.ts 类型定义，修改 ServiceDetailView 添加"调用"tab。

## Tasks

- [x] 1. 类型扩展和参数映射工具
  - [x] 1.1 Extend `frontend/src/renderer/types/service.ts`
    - Add `ServiceParamMeta` interface (name, type, required, defaultValue, description)
    - Add `InvocationHistoryEntry` interface (id, timestamp, mode, params, success, result, error)
    - _Requirements: 1.1, 5.3_

  - [x] 1.2 Create `frontend/src/renderer/utils/serviceParamMapping.ts`
    - Implement `mapParamToInput(paramType)` — maps Java type strings to ParamInputConfig
    - Export `ParamInputConfig` interface
    - Type mappings: String→text, Integer/Long→number(step=1), Float/Double/BigDecimal→number(step=any), Boolean→checkbox, Date→date, Timestamp→datetime-local, Time→time, Map/List→textarea, default→text
    - _Requirements: 2.2, 2.5_

  - [x]* 1.3 Write property tests for serviceParamMapping
    - **Property 7: Param type mapping correctness**
    - **Validates: Requirements 2.2, 2.5**

- [x] 2. Service Invoke Store
  - [x] 2.1 Create `frontend/src/renderer/stores/serviceInvoke.ts`
    - Define `ServiceInvokeState` interface with serviceName, loading, result, error, fieldErrors, successMessage, asyncJobId, asyncJobStatus, polling, pollingTimer, history
    - Implement `initialize(serviceName)` — sets context
    - Implement `invokeSync(params)` — calls serviceInvoker.invoke, handles success/failure lifecycle, adds to history
    - Implement `invokeAsync(params)` — calls serviceInvoker.invokeAsync, stores jobId, starts polling via setInterval (2s)
    - Implement `pollJobStatus()` — calls serviceInvoker.getJobStatus, stops on complete/fail
    - Implement `stopPolling()` — clears interval timer
    - Implement timeout: stop polling after 60s with "异步任务超时，请稍后查询"
    - Implement `loadHistoryEntry(entry)` — restores params and result from history
    - Implement `clearResult()` and `resetAll()`
    - History capped at 10 entries (FIFO)
    - Handle loading/error lifecycle: loading=true on start, clear result/error; loading=false on complete
    - Handle network errors with "网络错误，请重试" message
    - Preserve field-level validation errors in `fieldErrors`
    - _Requirements: 1.1–1.13, 5.1–5.3, 6.1–6.6_

  - [x]* 2.2 Write property tests for serviceInvoke store
    - **Property 1: Invocation call delegation**
    - **Property 2: Invocation start lifecycle**
    - **Property 3: Invocation success lifecycle**
    - **Property 4: Invocation failure lifecycle**
    - **Property 5: History size invariant**
    - **Property 6: Reset returns to initial state**
    - **Property 12: History entry completeness**
    - **Property 13: History entry restore**
    - **Validates: Requirements 1.2, 1.3, 1.5–1.13, 5.2, 5.3, 6.1, 6.3**

- [x] 3. Checkpoint - Core logic tests
  - Ensure all tests pass, ask the user if questions arise.

- [x] 4. ServiceParamForm and ServiceResultPanel components
  - [x] 4.1 Create `frontend/src/renderer/components/services/ServiceParamForm.vue`
    - Props: `params` (ServiceParamMeta[]), `formData` (Record), `fieldErrors` (FieldError[]), `disabled` (boolean)
    - Emits: `update:formData`, `clear`
    - Generate form inputs from params array using `mapParamToInput`
    - Pre-populate fields with defaultValue
    - Mark required params with visual indicator
    - Validate required fields are non-empty (prevent empty submission)
    - Display field-level errors next to corresponding inputs
    - "清空" button resets to defaults
    - JSON textarea for Map/List types
    - Dark theme support with `[data-theme="dark"]`
    - _Requirements: 2.1–2.7_

  - [x] 4.2 Create `frontend/src/renderer/components/services/ServiceResultPanel.vue`
    - Props: `result` (unknown), `error` (string|null), `fieldErrors` (FieldError[]), `loading` (boolean), `polling` (boolean), `asyncJobStatus` (string|null), `successMessage` (string|null)
    - Display result as formatted JSON (pre + JSON.stringify with indentation)
    - Display error with code, message, field-level details
    - Show _tx transaction metadata in collapsible section
    - "复制" button copies JSON to clipboard
    - Polling indicator when async job is active
    - Dark theme support
    - _Requirements: 3.1–3.6_

  - [x]* 4.3 Write property tests for ServiceParamForm
    - **Property 8: Form default value handling**
    - **Property 9: Required field validation**
    - **Property 10: Field-level error display**
    - **Validates: Requirements 2.3, 2.4, 2.6, 2.7**

- [x] 5. ServiceInvokeTab and ServiceDetailView integration
  - [x] 5.1 Create `frontend/src/renderer/components/services/ServiceInvokeTab.vue`
    - Props: `serviceName` (string), `inParams` (string[])
    - Orchestrates ServiceParamForm + ServiceResultPanel
    - On mount: initialize serviceInvoke store with serviceName
    - "同步调用" and "异步调用" buttons
    - Disable buttons while loading (Property 11)
    - Wire form data to store, invoke actions
    - Display history list, click to restore (loadHistoryEntry)
    - Success/error notification display
    - Dark theme support
    - _Requirements: 4.1–4.7, 5.1–5.2_

  - [x] 5.2 Modify `frontend/src/renderer/views/ServiceDetailView.vue`
    - Extend activeTab type to include 'invoke'
    - Add "调用" tab button in tab-bar
    - Import and render ServiceInvokeTab when activeTab === 'invoke'
    - Pass serviceName and inParams props
    - _Requirements: 4.1, 4.2_

  - [x]* 5.3 Write property tests for ServiceInvokeTab
    - **Property 11: Button disable while loading**
    - **Validates: Requirements 4.5, 6.5**

- [x] 6. Final checkpoint - Full integration
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties
- Unit tests validate specific examples and edge cases
- All new components follow existing dark theme pattern with `[data-theme="dark"]`
- All new components use `<script setup lang="ts">`
- 复用已有 `serviceInvoker.ts`，不新建 API 客户端
- 前端 only spec，无后端变更
