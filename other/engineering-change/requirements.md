---
name: engineering-change
category: other
description: Template for Engineering Change
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：工程变更管理 ({{SPEC_NAME_TITLE}} Management)

## 概述

新增工程变更通知（ECN）管理模块，支持变更记录 CRUD、影响范围记录、变更状态流转。对应原始需求 Requirement 2（工程变更管理）。扩展 `mfg` 子系统。

## 术语

- **ECN**: {{SPEC_NAME_TITLE}} Notice，工程变更通知
- **ChangeImpact**: 变更影响范围，记录受影响的 BOM/产品/工单

## 需求

### 需求 1: 变更通知管理

**用户故事**: 作为工程经理，我需要创建和管理工程变更通知，以便系统化跟踪产品变更。

#### 验收标准

1.1 系统应支持 ECN 的创建、查看、编辑、删除（CRUD）
1.2 ECN 应包含：变更编号、变更标题、变更类型（BOM变更/工艺变更/材料替代）、优先级、状态、发起人、描述、影响范围描述
1.3 变更编号应自动生成，格式为 ECN-YYYYMMDD-XXXX
1.4 ECN 列表应支持分页查询
1.5 ECN 列表应支持按状态筛选

### 需求 2: 变更状态流转

**用户故事**: 作为工程经理，我需要管理变更通知的审批和执行流程。

#### 验收标准

2.1 ECN 应支持以下状态：草稿(EcnDraft)、待审批(EcnPendingApproval)、已批准(EcnApproved)、执行中(EcnInProgress)、已完成(EcnCompleted)、已拒绝(EcnRejected)
2.2 系统应支持 ECN 状态变更操作
2.3 状态变更应记录变更时间

### 需求 3: 前端集成

**用户故事**: 作为用户，我需要通过前端界面管理工程变更通知。

#### 验收标准

3.1 ECN 管理应集成到 `mfg` 子系统导航中
3.2 应提供 ECN 列表、详情、创建/编辑表单
3.3 ECN 详情页应显示状态流转按钮
