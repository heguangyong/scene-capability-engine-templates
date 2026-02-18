---
name: wave4-coverage-batcha
category: other
description: Template for Wave4 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave4 覆盖率实施 Batch A（14-00）

## 设计目标

以轻量单元测试方式覆盖 `ServiceController` 的目录可用性、组件归属、路径定义与调用分支，保障服务 API 入口契约稳定。

## 实施策略

1. **扩展 `ServiceControllerWorkflowPropertyTest`**
   - 延续现有 Stub/反射模式，避免引入重型运行时依赖。
   - 新增目录缺失、参数异常、组件归属失败、详情缺失等 4xx/5xx 分支用例。

2. **新增 `ServiceControllerRouteExecutionPropertyTest`**
   - 通过 `Router.match()` 命中真实注册路由并执行 `RouteHandler`。
   - 覆盖固定组件定义入口与调用入口的 503/404/200 路径。

3. **覆盖重点**
   - `serviceHub` 响应结构稳定性。
   - `listAllServices` 的 `IllegalArgumentException -> 400` 映射。
   - `getComponentServiceByPath` / `getFixedComponentServiceDefinitionByPath` / `invokeFixedComponentByPath` 的关键分支。

4. **验证策略**
   - 定向运行本批次新增/扩展测试类。
   - 运行 `mvn -q -pl app -am test` 全链路回归。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `13-02` 的增量报告。

