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

# Design: KSE 文档治理与结构合规（11-00）

## 设计目标

以最小侵入方式修复历史文档结构违规，优先使用 KSE 官方治理命令，必要时进行可追踪的文件迁移与占位文件补齐。

## 治理策略

1. **先诊断，后变更**：使用 `kse docs diagnose` 获取违规基线。
2. **优先自动归档**：逐个 Spec 执行 `kse docs archive --spec <name>`，减少手工迁移错误。
3. **手工补齐兜底**：对于 `archive` 无法处理的目录（如非标准子目录），使用明确目录映射策略迁移。
4. **结构补齐**：为缺失必需文件的 Spec 创建最小模板文件。
5. **验证闭环**：执行 `kse docs diagnose` + `kse status` 确认结果。

## 目录映射规则

- `data/` → `docs/data/`
- `diagnostics/` → `reports/diagnostics/`
- `fixError/` → `reports/fixError/`
- `deliverables/` → `results/deliverables/`
- `patterns/` → `docs/patterns/`
- `config/` → `docs/config/`

说明：映射目标均在 KSE 允许的标准子目录之下，避免新建非标准顶层目录。

## 缺失文件模板

对缺失文件的 Spec，按如下最小模板创建：

- `requirements.md`：说明历史 Spec 补齐背景与目标。
- `design.md`：说明该 Spec 当前为历史补齐态、后续按需细化。
- `tasks.md`：给出最小可执行任务列表，默认标记为已补齐占位。

## 风险与回滚

- 风险：文档迁移可能影响既有引用路径。
- 缓解：只调整结构，不改正文内容；迁移后执行诊断并保留日志输出。
- 回滚：通过版本控制恢复（若仓库未初始化，依赖工作目录备份策略）。

## 验证方案

1. 执行 `kse docs diagnose`，确认 error/warning 显著下降。
2. 执行 `kse status`，确认文档合规状态改善。
3. 如命令可用，执行 `kse docs validate --all` 作为补充检查。

