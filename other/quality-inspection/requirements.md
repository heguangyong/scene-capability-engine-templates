---
name: quality-inspection
category: other
description: Template for Quality Inspection
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 质量检验管理 — 需求文档

## 概述

新增质量检验模块，关联生产工单，支持检验记录的创建、查看、结果判定。属于制造（MFG）子系统扩展。

---

## 用户故事

### US-1: 质量检验列表
作为生产管理员，我需要查看所有质量检验记录的列表，以便跟踪检验进度。

**验收标准:**
- AC-1.1: 列表显示检验编号、关联工单、产品名称、检验结果、检验日期
- AC-1.2: 支持按检验结果（全部/待检/通过/不通过/有条件通过）筛选
- AC-1.3: 支持分页，默认每页 20 条

### US-2: 创建质量检验
作为质检员，我需要为生产工单创建质量检验记录。

**验收标准:**
- AC-2.1: 必填字段：关联工单 ID、检验类型
- AC-2.2: 可选字段：检验员、备注、检验项目列表
- AC-2.3: 创建后状态为"待检"（QiPending）
- AC-2.4: 自动生成检验编号，格式 `QI-YYYYMMDD-XXXX`

### US-3: 质量检验详情
作为质检员，我需要查看检验记录的详细信息。

**验收标准:**
- AC-3.1: 显示检验基本信息、关联工单信息、产品信息
- AC-3.2: 显示检验项目列表及各项结果
- AC-3.3: 显示检验结论和备注

### US-4: 提交检验结果
作为质检员，我需要提交检验结果（通过/不通过/有条件通过）。

**验收标准:**
- AC-4.1: 只有"待检"状态的记录可以提交结果
- AC-4.2: 结果选项：Passed（通过）、Failed（不通过）、ConditionalPass（有条件通过）
- AC-4.3: 提交结果时可填写检验备注
- AC-4.4: 提交后记录检验完成时间

### US-5: 后端 REST API
作为前端应用，我需要完整的质量检验 REST API。

**验收标准:**
- AC-5.1: `GET /api/v1/{{SPEC_NAME}}s` — 列表查询，支持分页和结果筛选
- AC-5.2: `GET /api/v1/{{SPEC_NAME}}s/{id}` — 详情查询
- AC-5.3: `POST /api/v1/{{SPEC_NAME}}s` — 创建检验记录
- AC-5.4: `PUT /api/v1/{{SPEC_NAME}}s/{id}` — 更新检验记录（仅待检状态）
- AC-5.5: `DELETE /api/v1/{{SPEC_NAME}}s/{id}` — 删除检验记录（仅待检状态）
- AC-5.6: `PUT /api/v1/{{SPEC_NAME}}s/{id}/result` — 提交检验结果

### US-6: 导航集成
作为用户，我需要从制造子系统中访问质量检验模块。

**验收标准:**
- AC-6.1: 质量检验出现在 MFG 子系统的模块列表中
- AC-6.2: 侧边栏点击制造图标后，SubsystemPanel 显示质量检验入口
- AC-6.3: 路由 `/{{SPEC_NAME}}` 和 `/{{SPEC_NAME}}/:inspectionId` 正确注册

---

## 非功能需求

- NFR-1: 遵循现有代码模式（参考 ProductionRun 模块）
- NFR-2: 后端使用 Moqui WorkEffort entity 存储检验记录
- NFR-3: 前端使用 Vue 3 + Pinia + TypeScript
- NFR-4: 编译验证：后端 `mvn clean package -DskipTests`，前端 `npm run build`
