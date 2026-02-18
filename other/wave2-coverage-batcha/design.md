---
name: wave2-coverage-batcha
category: other
description: Template for Wave2 Coverage Batcha
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave2 覆盖率实施 Batch A（12-04）

## 设计目标

通过可控 stub 子类 + 反射方式覆盖业务主分支，避免引入真实 Moqui 运行时依赖。

## 实施策略

1. **EntityCrudService.batch**
   - 创建测试用子类重载 `create/update/delete`，可配置成功、失败与异常。
   - 覆盖以下路径：
     - `body == null`
     - `operations` 非法
     - 非 map operation
     - `op` 缺失
     - `create/update/delete` 参数缺失
     - `continueOnError=false` 立即 abort
     - `continueOnError=true` 汇总返回

2. **ServiceInvocationService**
   - 使用轻量子类重写 `invokeSync` 与 `getJobStatus`，隔离 `ExecutionContextFactory`。
   - 覆盖以下路径：
     - `invokeSyncWithTransaction`（`service` 默认策略）
     - `invokeSyncWithTransaction` 捕获 `IllegalArgumentException`
     - `invokeAsync` 任务创建与异步成功/失败状态更新
     - `getJobStatus` 命中 in-memory tracker

## 验证策略

1. 定向执行新增测试。
2. 执行 `mvn -q -pl app -am test`。
3. 执行 `scripts/quality-baseline.ps1`（SpecSlug=`{{SPEC_NAME}}`）并生成增量报告。

