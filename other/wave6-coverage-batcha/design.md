---
name: wave6-coverage-batcha
category: other
description: Template for Wave6 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave6 覆盖率实施 Batch A（16-00）

## 设计目标

通过反射 + 动态代理 + `Unsafe` 轻量构建对象图，补齐 `ServiceInvocationService#validateParameters` 的核心分支与静态辅助方法边界分支，避免引入重型运行时依赖。

## 实施策略

1. **新增 `ServiceInvocationServiceValidationPropertyTest`**
   - 基于轻量 `ExecutionContextFactory` 代理覆盖“非深度校验上下文”快速返回路径。
   - 使用 `Unsafe.allocateInstance` 构造 `ExecutionContextImpl`、`ServiceFacadeImpl`、`ServiceDefinition` 组合，覆盖：
     - 服务定义缺失（404）
     - unknown 参数（`VALIDATION_ERROR`）
     - `convertValidateCleanParameters` 返回 `null`
     - `MessageFacadeImpl` 错误回退 details 组装
     - `IllegalArgumentException` 与通用异常分支

2. **同文件反射补测静态辅助方法**
   - 反射调用 `isFailure` 与 `extractHttpStatus`，覆盖空输入、非 map、非 Integer、合法 Integer。

3. **验证策略**
   - 定向运行 `ServiceInvocationServiceValidationPropertyTest`。
   - 运行 `mvn -q -pl app -am test`。
   - 运行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并输出与 `15-02` 的增量报告。

