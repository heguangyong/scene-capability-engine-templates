---
name: api-coverage-assessment
category: other
description: Template for Api Coverage Assessment
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: 03-05 API Coverage Assessment

## 1. 设计目标

通过标准化评估模型，给出“当前 API 能力现状 + 缺口清单 + 实施优先级”。

## 2. 评估框架

1. 业务域盘点: 以模块为单位定义功能列表。
2. API 映射: 建立功能到接口的一对多关系。
3. 质量审计: 校验 RESTful 规范与安全要求。
4. 缺口分级: 按业务价值和实施成本排序。

## 3. 数据模型

映射矩阵最小字段:
- `domain`
- `feature`
- `api_endpoint`
- `method`
- `auth_required`
- `coverage_status` (`covered`/`partial`/`missing`)
- `priority`

## 4. 质量规则

1. 统一 URI 与资源命名。
2. 正确使用 HTTP 方法与状态码。
3. JWT 鉴权与权限边界清晰。
4. 支持分页、筛选、排序等通用能力。

## 5. 与主计划追溯

- 对应 `03-00` 需求: 2.1
- 上游依赖: 无
- 下游输入: `03-06`
