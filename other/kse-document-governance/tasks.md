---
name: kse-document-governance
category: other
description: Template for Kse Document Governance
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: KSE 文档治理与结构合规（11-00）

## Tasks

- [x] 1 建立治理 Spec 基线
  - [x] 1.1 创建 `requirements.md`
  - [x] 1.2 创建 `design.md`
  - [x] 1.3 创建 `tasks.md`

- [x] 2 根目录违规治理
  - [x] 2.1 处理根目录 `DOCUMENTATION_INDEX.md` 到合规目录
  - [x] 2.2 复查根目录无额外 markdown 违规

- [x] 3 misplaced artifacts 治理
  - [x] 3.1 逐个 Spec 执行 `kse docs archive --spec <name>`
  - [x] 3.2 对归档失败项进行手工迁移兜底

- [x] 4 invalid_subdirectory 治理
  - [x] 4.1 按目录映射规则迁移非标准目录到标准目录
  - [x] 4.2 清理空的非标准目录

- [x] 5 missing_required_file 补齐
  - [x] 5.1 为缺失 Spec 创建最小 `requirements.md`
  - [x] 5.2 为缺失 Spec 创建最小 `design.md`
  - [x] 5.3 为缺失 Spec 创建最小 `tasks.md`

- [x] 6 验证与收尾
  - [x] 6.1 执行 `kse docs diagnose`
  - [x] 6.2 执行 `kse status`
  - [x] 6.3 更新 `.kiro/steering/CURRENT_CONTEXT.md`
