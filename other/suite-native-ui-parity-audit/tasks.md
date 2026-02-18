---
name: suite-native-ui-parity-audit
category: other
description: Template for Suite Native Ui Parity Audit
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：三大应用原生 UI 行为对齐审计（64-06）

- [x] 1 后端审计接口落地
  - [x] 1.1 在 `ArchitectureGovernanceController` 注册 parity-audit 路由
  - [x] 1.2 在 `ArchitectureGovernanceService` 实现审计概览聚合
  - [x] 1.3 回填后端服务与控制器测试

- [x] 2 前端审计看板集成
  - [x] 2.1 扩展 `suiteHub` 类型与 API 服务
  - [x] 2.2 在 `SuiteHubView` 增加审计概览面板
  - [x] 2.3 统一刷新链路并处理错误态

- [x] 3 治理与证据闭环
  - [x] 3.1 执行后端定向测试
  - [x] 3.2 执行前端构建
  - [x] 3.3 执行 KSE 校验、模板导出与双轨同步

