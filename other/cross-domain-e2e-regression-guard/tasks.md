---
name: cross-domain-e2e-regression-guard
category: other
description: Template for Cross Domain E2e Regression Guard
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：跨域 E2E 回归守护（63-02）

- [x] 1 维护跨域场景目录
  - [x] 1.1 复核既有场景可执行性
  - [x] 1.2 新增场景并分配优先级

- [x] 2 执行跨域回归批次
  - [x] 2.1 按变更影响选择场景
  - [x] 2.2 执行验证并记录结果
  - [x] 2.3 失败场景给出修复闭环

- [x] 3 落盘与汇总证据
  - [x] 3.1 生成场景级证据文档
  - [x] 3.2 将结果同步到总控与 handoff

- [x] 4 治理收口
  - [x] 4.1 更新 `CURRENT_CONTEXT.md` 中 E2E 状态
  - [x] 4.2 更新风险项与下一批建议
