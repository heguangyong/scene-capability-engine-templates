---
name: groovy-usage-deep-analysis
category: other
description: Template for Groovy Usage Deep Analysis
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Tasks: 03-01 {{SPEC_NAME_TITLE}}

## 任务列表

- [x] 1 建立 Groovy 基线清单
  - [x] 1.1 扫描 `backend` 内全部 `.groovy` 文件并按生产/测试分类
  - [x] 1.2 输出模块维度统计（数量、LOC、占比）
  - [x] 1.3 产出 `reports/groovy-inventory.md`
  - _Validates: Requirements 3.1_

- [x] 2 识别 XML 嵌入逻辑
  - [x] 2.1 扫描 XML 中 `actions/script` 逻辑片段
  - [x] 2.2 输出可迁移性标注（直接迁移/需重构）
  - [x] 2.3 产出 `reports/xml-actions-analysis.md`
  - _Validates: Requirements 3.2_

- [x] 3 生成依赖与风险画像
  - [x] 3.1 梳理高风险服务调用链
  - [x] 3.2 形成风险矩阵（复杂度/关键度/替代可行性）
  - [x] 3.3 产出 `reports/risk-matrix.md`
  - _Validates: Requirements 3.3_

- [x] 4 输出迁移优先级
  - [x] 4.1 形成 P0/P1/P2 迁移队列
  - [x] 4.2 给出 `03-02` 首批迁移候选（20%-30%）
  - [x] 4.3 产出 `results/migration-priority.csv`
  - _Validates: Requirements 3.4_
