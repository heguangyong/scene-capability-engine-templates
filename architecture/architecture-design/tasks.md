---
name: architecture-design
category: other
description: Template for Architecture Design
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

﻿# Implementation Plan: {{SPEC_NAME_TITLE}}（07-01）

## Tasks

- [x] 1 补齐 Spec 结构文件
  - [x] 1.1 补齐 `tasks.md`

- [x] 2 结构治理兼容
  - [x] 2.1 保持 `requirements.md` 与 `design.md` 不变
  - [x] 2.2 维持标准子目录结构

- [x] 3 按需扩展
  - [x] 3.1 如重启该 Spec，细化实现任务与验收标准
    - [x] 3.1.1 建立 Requirements(1-12) -> Design -> Work Package 追踪矩阵
    - [x] 3.1.2 定义重启后 6 个可执行工作包（WP1-WP6）
    - [x] 3.1.3 为每个工作包补充输入、输出、实施步骤、验收标准
    - [x] 3.1.4 定义阶段门禁（Go/No-Go）和回滚检查点
    - [x] 3.1.5 定义测试与质量基线（单元/集成/E2E/属性/性能/回归）
    - [x] 3.1.6 定义风险监控与预警指标

## 3.1 细化结果（Spec 重启执行包）

### WP1: 架构保留基线与边界确认（Requirement 1, 2）
**输入**: `requirements.md` R1-R2，`design.md` Architecture/Components 章节  
**输出**:
- `reports/preservation-baseline.md`
- `reports/component-boundary-checklist.md`

**实施步骤**:
1. 列出必须保留组件（Entity/Service Engine/Tx/Cache/Security/DB Abstraction）
2. 列出必须保留的 Component System（XML Screen/Form/Widget/Section/Libs）
3. 输出替换禁区和可扩展接口清单

**验收标准**:
1. 保留组件清单覆盖 R1-R2 的全部验收项
2. 明确“禁止替换”与“允许扩展”的边界，无冲突条目
3. 与 `design.md` 高层架构图一致

### WP2: REST API 与 OpenAPI 合同设计包（Requirement 3, 5）
**输入**: `requirements.md` R3、R5，`design.md` REST API Layer 章节  
**输出**:
- `api/openapi-v1.yaml`（骨架与域示例）
- `reports/api-contract-guidelines.md`

**实施步骤**:
1. 定义资源模型、URL 规范、HTTP 动词、状态码标准
2. 设计鉴权/授权方案（与 Moqui Security 对齐）
3. 定义错误模型、分页/过滤/排序规范
4. 输出 OpenAPI 3.0 基础合同和示例域（如 Order）

**验收标准**:
1. OpenAPI 文件可通过结构校验
2. 合同覆盖 JSON request/response、认证、错误响应
3. 合同映射到 Moqui Service/Entity 调用路径

### WP3: Groovy -> Java 迁移蓝图（Requirement 4）
**输入**: `requirements.md` R4，`design.md` Java Service Layer 章节  
**输出**:
- `reports/groovy-java-migration-plan.md`
- `reports/groovy-java-equivalence-checklist.md`

**实施步骤**:
1. 建立 88 个 Groovy 文件清单与优先级分组
2. 定义 Java 接口/实现/依赖注入模板
3. 定义语义等价转换规则（类型、集合、模板语法等）
4. 定义回归验证策略（行为一致性 + 性能基线）

**验收标准**:
1. 迁移清单包含优先级、依赖关系、批次划分
2. 每类 Groovy 模式至少有 1 个 Java 转换模板
3. 等价性检查清单可直接用于代码评审与测试

### WP4: 前后端分离与渲染迁移可行性包（Requirement 6, 7, 8, 10）
**输入**: `requirements.md` R6-R8、R10，`design.md` Frontend Rendering + Vue/Electron 章节  
**输出**:
- `reports/frontend-backend-boundary.md`
- `reports/rendering-migration-feasibility.md`
- `prototypes/screen-render-poc/`（POC 说明与截图/结果）

**实施步骤**:
1. 定义前后端边界和 API-only 通信约束
2. 设计 XML Screen -> Vue 解释器映射策略
3. 设计 FreeMarker Macro -> Vue 组件/组合式 API 映射
4. 完成一个关键业务屏幕 POC（含 HTML/CSV/XML/QVT 导出路径）
5. 形成 Go/No-Go 建议

**验收标准**:
1. 边界文档明确“前端不直连数据库”等硬性约束
2. 至少 1 个完整屏幕 POC 可运行并记录性能
3. 可行性报告包含风险、复杂度、工作量、决策条件

### WP5: 渐进迁移与回滚治理包（Requirement 9）
**输入**: `requirements.md` R9，`design.md` Migration Strategy/Rollback 章节  
**输出**:
- `reports/migration-phases-and-gates.md`
- `reports/rollback-playbook.md`

**实施步骤**:
1. 固化 6 个阶段交付项和阶段成功标准
2. 定义新旧实现共存与流量切换策略
3. 为每阶段定义回滚触发条件、步骤、验证清单
4. 定义阶段间测试门禁

**验收标准**:
1. 每阶段均有可验证的进入/退出条件
2. 每阶段均有独立回滚手册且可演练
3. 迁移过程中系统可持续对外服务

### WP6: 风险与质量保障包（Requirement 11, 12）
**输入**: `requirements.md` R11-R12，`design.md` Risk Mitigation 章节  
**输出**:
- `reports/risk-register.md`
- `reports/quality-test-strategy.md`

**实施步骤**:
1. 建立技术/性能/安全/连续性风险台账（概率-影响矩阵）
2. 定义预警指标、监控方法、应急预案
3. 定义测试分层：单元、集成、E2E、属性、性能、回归
4. 定义关键路径覆盖率目标和发布门禁

**验收标准**:
1. 高风险项全部有应对策略与责任人
2. 测试策略覆盖 R12 全部验收条目
3. 发布门禁可执行且可审计（含测试与性能阈值）

## 重启执行顺序（建议）

1. `WP1` 架构基线冻结（先明确保留边界）
2. `WP2` API 合同先行（后续前后端解耦基础）
3. `WP3` 服务迁移蓝图（支撑 API 实现）
4. `WP4` 前端渲染迁移 POC（尽早验证高风险）
5. `WP5` 阶段计划与回滚机制落地
6. `WP6` 风险与质量门禁闭环

## Go/No-Go 阶段门禁

| Gate | 触发时点 | 必要条件 |
|------|----------|----------|
| G1 | WP1 完成 | 保留边界清单经评审通过，无核心替换争议 |
| G2 | WP2 完成 | OpenAPI 合同通过校验，认证/错误模型完整 |
| G3 | WP3 完成 | 88 文件迁移计划可执行，等价验证清单可落地 |
| G4 | WP4 完成 | 渲染 POC 达到功能可用，形成 Go/No-Go 结论 |
| G5 | WP5 完成 | 阶段门禁和回滚手册可演练 |
| G6 | WP6 完成 | 风险台账与质量门禁就绪，可进入实施 Spec |
