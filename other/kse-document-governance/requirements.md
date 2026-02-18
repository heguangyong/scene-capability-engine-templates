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

# Requirements: KSE 文档治理与结构合规（11-00）

## 概述

本 Spec 目标是清理历史文档结构违规，恢复 `.kiro/` 文档治理合规性，确保后续 Spec 迭代稳定。

## 功能需求

### Requirement 1: 根目录文档合规

**用户故事:** 作为项目维护者，我希望项目根目录不存在违规 Markdown 文档，以便通过 KSE 根目录检查。

#### 验收标准

1. WHEN 执行文档治理修复 THEN 系统 SHALL 处理根目录违规 Markdown（`DOCUMENTATION_INDEX.md`）到合规位置。
2. WHEN 再次执行文档诊断 THEN 系统 SHALL 不再报告 root markdown 违规。

### Requirement 2: Spec 工件归档合规

**用户故事:** 作为项目维护者，我希望所有 Spec 根目录下的附加工件都归档到标准子目录（`reports/`、`scripts/`、`tests/`、`results/`、`docs/`），以便治理工具持续通过。

#### 验收标准

1. WHEN 执行归档治理 THEN 系统 SHALL 将所有 misplaced artifacts 迁移到标准子目录。
2. WHEN 迁移完成 THEN 系统 SHALL 保留 `requirements.md`、`design.md`、`tasks.md` 在 Spec 根目录。

### Requirement 3: 非标准子目录治理

**用户故事:** 作为项目维护者，我希望所有 Spec 顶层子目录符合 KSE 白名单，以避免 invalid_subdirectory 告警。

#### 验收标准

1. WHEN 执行目录治理 THEN 系统 SHALL 将 `data/`、`diagnostics/`、`fixError/`、`deliverables/`、`patterns/`、`config/` 等非标准目录迁移到标准目录之下。
2. WHEN 再次执行诊断 THEN 系统 SHALL 不再报告 invalid_subdirectory。

### Requirement 4: 必需文件补齐

**用户故事:** 作为项目维护者，我希望历史 Spec 缺失的必需文件被补齐，以便所有 Spec 均满足最小结构规范。

#### 验收标准

1. WHEN 执行治理 THEN 系统 SHALL 为缺失 Spec 创建最小可读的 `requirements.md`、`design.md`、`tasks.md`。
2. WHEN 再次执行诊断 THEN 系统 SHALL 不再报告 missing_required_file。

### Requirement 5: 可追踪的治理记录

**用户故事:** 作为项目维护者，我希望治理过程可追踪并可恢复，以便后续会话持续接管。

#### 验收标准

1. WHEN 治理完成 THEN 系统 SHALL 更新当前 Spec 的 `tasks.md` 状态。
2. WHEN 会话结束 THEN 系统 SHALL 更新 `.kiro/steering/CURRENT_CONTEXT.md` 记录治理结果与后续建议。
3. WHEN 完成收尾 THEN 系统 SHALL 至少执行一次 `kse status`。

