---
name: template-library-regression-guard
category: other
description: Template for Template Library Regression Guard
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：模板库回归守护（63-04）

- [x] 1 建立模板对账机制
  - [x] 1.1 清点导出模板与仓库模板
  - [x] 1.2 识别缺失/重复/版本漂移项

- [x] 2 建立模板发布校验机制
  - [x] 2.1 固化 `templates update/search/show` 检查流程
  - [x] 2.2 将结果写入证据与摘要文件

- [x] 3 建立模板兼容复核机制
  - [x] 3.1 每次 KSE 升级后执行模板复核
  - [x] 3.2 异常项写入风险台账并跟踪关闭

- [x] 4 治理收口
  - [x] 4.1 更新 handoff 模板状态统计
  - [x] 4.2 回填 `CURRENT_CONTEXT.md` 与下一批计划
