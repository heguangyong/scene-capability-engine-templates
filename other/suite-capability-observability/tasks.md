---
name: suite-capability-observability
category: other
description: Template for Suite Capability Observability
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：三大应用能力对齐可观测（64-05）

- [x] 1 补齐后端能力概览接口
  - [x] 1.1 在 `ArchitectureGovernanceController` 注册 Suite Hub 概览路由
  - [x] 1.2 在 `ArchitectureGovernanceService` 实现三大应用能力汇总
  - [x] 1.3 回填服务与控制器测试

- [x] 2 补齐前端可观测展示
  - [x] 2.1 新增 `suiteHub` 类型定义与 API 服务
  - [x] 2.2 在 `SuiteHubView` 增加能力总览面板
  - [x] 2.3 增加覆盖率标签、刷新和错误处理

- [x] 3 执行验证与治理回填
  - [x] 3.1 执行后端定向测试
  - [x] 3.2 执行前端构建
  - [x] 3.3 执行 KSE 校验与模板导出

