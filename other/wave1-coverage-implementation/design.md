---
name: wave1-coverage-implementation
category: other
description: Template for Wave1 Coverage Implementation
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Wave1 覆盖率实施（12-01）

## 设计目标

用低耦合、低风险的单元/属性测试优先覆盖纯函数逻辑与路由契约逻辑，避免引入重型集成依赖。

## 实施策略

1. **EntityCatalogService**
   - 通过反射覆盖静态纯函数：
     - `packageNameOf`
     - `filter`
     - `normalizeComponentName`
   - 验证合法/非法输入、过滤行为边界。

2. **ServiceCatalogService**
   - 覆盖 `normalizeComponentName` 与 `filterServices` 相关行为（通过反射或现有公开方法路径）。

3. **OpenApiGenerator**
   - 构造最小 Swagger2 输入，覆盖：
     - info fallback
     - security scheme 注入
     - body parameter -> requestBody 转换
     - paths default fallback

## 验证策略

1. 先跑新增测试的定向命令。
2. 再跑 `mvn -q -pl app -am test`。
3. 最后跑 `scripts/quality-baseline.ps1` 获取覆盖率增量。

