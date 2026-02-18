---
name: production-run
category: other
description: Template for Production Run
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：生产工单管理 ({{SPEC_NAME_TITLE}} / Work Order)

## 简介

生产工单是制造子系统的核心功能，连接 BOM（物料清单）、工艺路线（Routing）和库存管理。用户创建生产工单后，系统根据 BOM 计算所需物料，按工艺路线安排工序，跟踪生产进度，完工后将成品入库。本 Spec 实现生产工单的基础 CRUD 和状态流转。

## 术语表

- **{{SPEC_NAME_TITLE}}（生产工单）**: 一次生产任务，指定要生产的产品、数量、使用的 BOM 和工艺路线
- **Work Order（工单）**: 同 {{SPEC_NAME_TITLE}}，本项目中两者等价
- **Run Status（工单状态）**: Created → Scheduled → Running → Completed / Cancelled
- **Run Task（工序任务）**: 生产工单中的一道工序，对应工艺路线中的一个步骤

## 需求

### 需求 1：生产工单 CRUD

**用户故事：** 作为生产管理员，我希望能创建、查看、编辑和删除生产工单，以便管理生产任务。

#### 验收标准

1. THE System SHALL 提供生产工单列表 API（GET /api/v1/{{SPEC_NAME}}s），支持分页和按状态筛选
2. THE System SHALL 提供生产工单详情 API（GET /api/v1/{{SPEC_NAME}}s/{id}），返回工单信息及其工序任务列表
3. THE System SHALL 提供创建生产工单 API（POST /api/v1/{{SPEC_NAME}}s），必填字段：productId、quantity、bomId
4. THE System SHALL 提供更新生产工单 API（PUT /api/v1/{{SPEC_NAME}}s/{id}），仅 Created 状态可编辑
5. THE System SHALL 提供删除生产工单 API（DELETE /api/v1/{{SPEC_NAME}}s/{id}），仅 Created 状态可删除
6. WHEN 创建生产工单时, THE System SHALL 自动生成工单编号（格式：PR-YYYYMMDD-XXXX）

### 需求 2：工单状态流转

**用户故事：** 作为生产管理员，我希望能推进工单状态，以便跟踪生产进度。

#### 验收标准

1. THE System SHALL 支持以下状态流转：Created → Scheduled → Running → Completed
2. THE System SHALL 支持取消操作：Created/Scheduled → Cancelled
3. WHEN 工单状态变更时, THE System SHALL 记录变更时间
4. THE System SHALL 拒绝无效的状态转换（如 Completed → Running）并返回 400 错误

### 需求 3：前端列表视图

**用户故事：** 作为生产管理员，我希望在前端查看生产工单列表，以便快速了解生产情况。

#### 验收标准

1. THE ProductionRunListView SHALL 显示工单列表，包含工单编号、产品名称、数量、状态、创建时间
2. THE ProductionRunListView SHALL 支持按状态筛选（全部/已创建/已排程/生产中/已完成/已取消）
3. THE ProductionRunListView SHALL 支持分页浏览
4. THE ProductionRunListView SHALL 提供创建新工单的入口

### 需求 4：前端详情视图

**用户故事：** 作为生产管理员，我希望查看工单详情和推进状态，以便管理单个生产任务。

#### 验收标准

1. THE ProductionRunDetailView SHALL 显示工单基本信息（编号、产品、数量、BOM、状态、时间）
2. THE ProductionRunDetailView SHALL 显示工序任务列表（来自关联的工艺路线）
3. THE ProductionRunDetailView SHALL 提供状态推进按钮（根据当前状态显示可用操作）
4. THE ProductionRunDetailView SHALL 提供编辑和删除按钮（仅 Created 状态）

### 需求 5：前端创建/编辑对话框

**用户故事：** 作为生产管理员，我希望通过表单创建和编辑生产工单。

#### 验收标准

1. THE ProductionRunFormDialog SHALL 提供产品选择（下拉列表，从产品 API 获取）
2. THE ProductionRunFormDialog SHALL 提供 BOM 选择（根据选中产品筛选可用 BOM）
3. THE ProductionRunFormDialog SHALL 提供数量输入（正整数，必填）
4. THE ProductionRunFormDialog SHALL 提供可选的计划开始日期和备注字段
5. THE ProductionRunFormDialog SHALL 在提交前验证必填字段

### 需求 6：导航集成

**用户故事：** 作为用户，我希望能从侧边栏访问生产工单管理。

#### 验收标准

1. THE router SHALL 注册 /{{SPEC_NAME}}（列表）和 /{{SPEC_NAME}}/:runId（详情）路由
2. THE 生产工单模块 SHALL 在导航系统中注册（NavId、PanelContext、componentMap、ContextPanel 面板映射）
3. THE ProductionRunPanel SHALL 作为 ContextPanel 的二级面板，显示工单相关的快捷操作
