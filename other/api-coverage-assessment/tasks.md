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

# Tasks: 03-05 API Coverage Assessment

## 任务列表

- [x] 1 建立功能-API 映射
  - [x] 1.1 梳理核心业务功能目录
  - [x] 1.2 映射现有 REST API 覆盖关系
  - [x] 1.3 产出 `results/feature-api-matrix.csv`
  - _Validates: Requirements 3.1_

- [x] 2 审计 API 设计质量
  - [x] 2.1 按 RESTful 规范检查接口设计
  - [x] 2.2 审查鉴权、分页、错误码一致性
  - [x] 2.3 产出 `reports/api-quality-audit.md`
  - _Validates: Requirements 3.2_

- [x] 3 形成缺口清单与优先级
  - [x] 3.1 标记缺失 API 与部分覆盖能力
  - [x] 3.2 定义 P0/P1/P2 补齐优先级
  - [x] 3.3 产出 `results/api-gap-priority.md`
  - _Validates: Requirements 3.3_

- [x] 4 完成交接输入
  - [x] 4.1 汇总 `03-06` 首批执行范围
  - [x] 4.2 补充接口规范约束
  - [x] 4.3 归档 `results/03-06-handoff.md`
  - _Validates: Requirements 3.1, 3.2, 3.3_
