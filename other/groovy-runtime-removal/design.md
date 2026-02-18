---
name: groovy-runtime-removal
category: other
description: Template for Groovy Runtime Removal
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: 03-04 {{SPEC_NAME_TITLE}}

## 1. 设计目标

在不影响现网能力的前提下，完成 Groovy 运行时“可验证移除”。

## 2. 执行顺序

1. 盘点依赖与配置引用。
2. 分层清理（构建层 -> 运行层 -> 文档层）。
3. 逐步验证（编译、启动、功能、性能）。

## 3. 关键技术点

### 3.1 构建层

- 调整 `build.gradle`/`pom.xml` 中 Groovy 相关依赖。
- 区分生产依赖与测试依赖，避免误删测试能力。

### 3.2 运行层

- 清理 Groovy 运行入口和注册配置。
- 确认服务执行已全部切换至 Java 实现。

### 3.3 验证层

- 启动验证: 服务启动、健康检查。
- 回归验证: 核心业务流程。
- 性能验证: 关键接口对比。

## 4. 与主计划追溯

- 对应 `03-00` 需求: 1.3
- 上游依赖: `03-03`
- 下游输入: `03-09`
