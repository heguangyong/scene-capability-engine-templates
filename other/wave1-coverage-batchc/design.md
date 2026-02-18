---
name: wave1-coverage-batchc
category: other
description: Template for Wave1 Coverage Batchc
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave1 覆盖率实施 Batch C（12-03）

## 设计目标

通过控制器公开流程路径测试提升有效覆盖，覆盖成功/失败/参数异常分支，同时保持测试轻量与可维护。

## 实施策略

1. **ServiceController 工作流测试**
   - 基于反射调用私有处理器方法。
   - 通过轻量 stub `RequestContext` 记录 `json/success/error/noContent` 输出。
   - 通过 `java.lang.reflect.Proxy` 构造 `ServiceCatalogService` 与 `ServiceInvocationService` 交互桩，覆盖：
     - catalog 未初始化（503）
     - catalog 业务校验失败（400/404）
     - invoke 同步/异步路径与事务头异常（400）
     - getJobStatus 成功/失败状态码分支

2. **EntityController 工作流测试**
   - 反射调用 `listEntitiesInternal`、`getEntityDefinition`、`list`、`delete`、`sendResult` 等关键路径。
   - 通过 `EntityCatalogService`/`EntityCrudService` 的反射字段注入轻量子类桩覆盖：
     - catalog 未初始化（503）
     - list 参数异常（400）
     - definition not found（404）
     - delete null -> `noContent()` 分支

3. **测试风格**
   - 继续沿用 jqwik + JUnit5。
   - 重点覆盖控制流分支，不在 Batch C 引入 servlet 容器/真实 Moqui runtime。

## 验证策略

1. 定向执行 Batch C 新增测试。
2. 执行 `mvn -q -pl app -am test`。
3. 执行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并生成增量报告。

