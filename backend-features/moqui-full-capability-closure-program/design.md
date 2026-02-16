---
name: moqui-full-capability-closure-program
category: backend-features
description: Template for Moqui Full Capability Closure Program
tags: [moqui, full, capability, closure, orchestration]
author: FallingAKS
created_at: '2026-02-16'
updated_at: '2026-02-16'
version: 1.0.0
kse_version: 1.47.7
---

# 设计文档：Moqui 全能力补齐与模板化总控（62-00）

## 1. 设计目标

`62-00` 负责“最终补齐”与“模板沉淀”，不只做状态同步：

1. 把历史“部分实现”能力推进到“可验收完成”
2. 把 `331-poc` 评估缺口收敛为可执行子 Spec 图谱
3. 把已验证方法固化成模板，支持多 agent 长周期推进

---

## 2. 输入证据与边界

- 需求基线：`.kiro/specs/01-00-customer-requirements/requirements.md`
- 既有对齐基线：`.kiro/specs/60-00-moqui-capability-parity-program/docs/moqui-capability-baseline-2026-02-14.md`
- Wave B 总控：`.kiro/specs/61-00-wave-b-enterprise-hardening-program/*`
- 当前上下文：`.kiro/steering/CURRENT_CONTEXT.md`
- 代码事实：
  - 后端控制器：`backend/app/src/main/java/org/moqui/rest/controller/*.java`（当前 36）
  - 前端路由：`frontend/src/renderer/router/index.js`（当前 71）
  - 功能视图：`frontend/src/renderer/views/*.vue`

---

## 3. 差距模型（Gap Taxonomy）

### 3.1 业务深度差距（Domain Depth）

- 已有模块但规则深度不足（审批、联动、结算、追溯、策略参数化）

### 3.2 跨域链路差距（Cross-Domain Flow）

- 端到端流程未闭环（销售→计划→采购/生产→发运→开票→成本）

### 3.3 平台治理差距（Platform & Ops）

- 可观测、部署治理、国产化实证、升级兼容、文档化资产

---

## 4. 子 Spec 图谱（62 系列）

建议在本总控下拆分以下子 Spec（按优先级）：

1. `62-01-moqui-capability-itemized-parity-matrix`
2. `62-02-domain-depth-gap-closure`
3. `62-03-cross-domain-e2e-flow-closure`
4. `62-04-platform-ops-governance-closure`
5. `62-05-331-gap-zero-closure`
6. `62-06-spec-template-productization`

> 原则：每个子 Spec 都必须提供 `requirements/design/tasks` 与可复验产物。

---

## 5. 执行编排（Master/Sub）

### 5.1 Master 职责

- 维护统一差距台账与优先级
- 管控并发/锁/回退策略
- 做跨 Spec 收敛与最终验收

### 5.2 Sub 职责

- 在单一子 Spec 范围内实现并自证
- 提交可追溯证据（构建、测试、API、UI、文档）
- 每轮回填 `CURRENT_CONTEXT.md`

### 5.3 并发与限流

- 默认：`maxParallel=1`
- 稳定后：`1 -> 2 -> 3` 逐级提升
- 出现 429：自动降并发并重跑当前批次

---

## 6. 模板产物设计（Goal-3）

模板目录建议：

- `requirements.md`：能力目标 + 验收标准
- `design.md`：差距模型 + 执行拓扑 + 风险回退
- `tasks.md`：阶段任务 + 证据任务 + DoD
- `docs/`：能力矩阵、scene 合同、验收报告、运行手册

模板最小能力：

1. 支持按“能力项”拆解而非仅模块拆解
2. 支持主从多 agent 并行推进
3. 支持上下文自动增量回填

---

## 7. 完成定义（Definition of Done）

`62-00` 完成时需满足：

1. 已形成并批准 `62-01 ~ 62-06` 执行图谱
2. 差距矩阵可追踪到能力项级，且 P0/P1 缺口清零
3. 模板产物可被 `kse templates` 发现和复用
4. `kse status --verbose`、`kse doctor --docs` 均通过
5. `CURRENT_CONTEXT.md` 完整记录本轮结果与下一批计划
