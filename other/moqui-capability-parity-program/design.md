---
name: moqui-capability-parity-program
category: other
description: Template for Moqui Capability Parity Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 能力对齐迁移总控（60-00）

## 1. 设计目标

在不重写业务目标的前提下，建立“原 Moqui 能力 → 新架构实现”的可执行迁移框架：

1. 先评估再开发（Assessment First）
2. Scene 合同化执行（Scene-Driven）
3. Master/Sub-Spec 并行编排（Collab + Orchestrate）
4. 每批次可复盘（CURRENT_CONTEXT 持续更新）

---

## 2. 基线输入（Evidence Sources）

- 原始需求基线：`.kiro/specs/01-00-customer-requirements/requirements.md`
- 后端实现入口：`backend/app/src/main/java/org/moqui/rest/RestApplication.java`
- 后端能力边界：`backend/app/src/main/java/org/moqui/rest/controller/*.java`
- 前端功能入口：`frontend/src/renderer/router/index.js`、`frontend/src/renderer/views/*.vue`
- 组件资产基线：
  - `backend/framework`：`entity=20, service=44, screen=12`
  - `backend/runtime/component`：`entity=34, service=80, screen=0`

> 说明：以上计数来自仓内文件扫描，用于迁移规模估算，不等价于业务完成度。

---

## 3. 现状能力分层（As-Is）

### 3.1 已落地模块（新架构）

- 业务模块：`party/product/order/facility/invoice/shipment/procurement/inventory/bom/routing/production-run/quality-inspection/engineering-change/equipment/maintenance-order/employee/department/return/calendar`
- 平台模块：`auth/admin/dashboard/search`
- Explorer/API 目录：`screens/entities/services` + `/docs` + `/openapi.json`

### 3.2 主要缺口类型

1. **深能力缺口**：已有模块但深业务规则未覆盖（如 MRP/MPS、ECN 全量影响分析、三方匹配、审批流）
2. **模块级缺口**：项目管理、工装夹具、完整服务保障域尚未成体系
3. **平台型缺口**：可配置工作流、报表中心、数据迁移工具链、国产环境实测闭环

---

## 4. 目标架构（To-Be）

### 4.1 Spec 图谱（Master + 子 Spec）

- Master：`{{SPEC_NAME}}`
- 子 Spec（建议批次）：
  - Wave A（P0 核心经营主链）
    - `60-01` 主数据深能力（多组织属性、变体 BOM、替代策略）
    - `60-02` 销售-发运-开票联动与审批
    - `60-03` 生产计划与执行增强（MPS/MRP/能力平衡）
    - `60-04` 采购库存深能力（来源清单、配额、三方匹配）
    - `60-05` 成本核算增强（标准/实际/差异）
  - Wave B（P1 支撑域）
    - `60-06` 项目管理（Project/WBS/成本归集）
    - `60-07` 服务保障（通知/维修订单/返修成本）
    - `60-08` 工装夹具（借还/校准/追踪）
    - `60-09` 设备管理增强（分类/预防性维护/租赁）
  - Wave C（P2 平台与治理）
    - `60-10` 工作流与审批引擎统一
    - `60-11` 报表与审计运营能力
    - `60-12` 数据迁移与国产环境验证闭环

### 4.2 Scene 合同策略

- 每个子 Spec 至少 1 个 scene manifest，定义：
  - 意图与边界
  - 输入数据模型
  - 输出契约（API、UI、测试、文档）
  - 风险与幂等要求
- Master scene 负责路由与收敛，不直接承担细节开发。

---

## 5. 主从 Agent 执行模型

### 5.1 主 Agent（Master）

- 维护总 backlog、优先级和依赖
- 分配子 Spec 到从 Agent
- 统一做合并冲突处理、集成回归、文档治理、上下文更新

### 5.2 从 Agent（Sub）

- 在指定子 Spec 范围内独立实现（代码+测试+文档）
- 输出可验收证据（测试日志、构建结果、关键截图/报告）
- 不跨越未授权的 Spec 范围修改共享文件

### 5.3 并发与限流控制

- 默认：`maxParallel=1`（稳态）
- 批次提并发策略：`1 → 2 → 3`（每次提并发前先验证 429 风险）
- 出现 `429 Too Many Requests` 时自动降并发并重试当前批次

---

## 6. 验收门禁（Definition of Done）

每个子 Spec 完成必须满足：

1. `requirements.md/design.md/tasks.md` 完整
2. 代码构建通过 + 定向测试通过
3. 对应 API/docs/路由/导航闭环
4. `kse doctor --docs` 合规
5. `CURRENT_CONTEXT.md` 更新本轮结果与风险

