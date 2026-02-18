---
name: capability-sustainment-program
category: other
description: Template for Capability Sustainment Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：能力持续闭环总控（63-00）

## 概述

`63-00` 作为 `62-00` 之后的持续运行总控，目标是将“已完成闭环”升级为“可持续不回退”：

1. 保持 Moqui 能力对齐基线（`Full=169 / Partial=0 / OutOfScope=3`）  
2. 保持 331 缺口清零基线（`P0/P1/P2=0`）  
3. 保持模板库与官方源可发现性基线（导出/上传/可检索/可展示）  
4. 用主从协作机制持续处理新增需求，不破坏既有闭环

---

## 需求 1：维持能力矩阵不回退

**用户故事**：作为项目 owner，我希望每一轮功能变更都不会把已闭环能力打回 `Partial`。

### 验收标准

1.1 每轮变更后回填 `62-01` 能力矩阵并更新统计  
1.2 如出现回退项，必须在同批次完成修复或挂入 P0  
1.3 输出“本轮能力变更清单 + 证据路径”  
1.4 `kse status --verbose` 可追踪本轮任务状态

---

## 需求 2：维持跨域链路与平台治理基线

**用户故事**：作为交付负责人，我希望业务链路与运维治理在持续迭代中稳定可用。

### 验收标准

2.1 核心跨域 E2E 场景保持可复验，新增链路按场景规范补录  
2.2 运维/治理 runbook 保持最新（部署、回滚、监控、审计）  
2.3 每轮至少补充一份可复盘证据到 `docs/handoffs/evidence`  
2.4 高并发 429 风险受控（默认低并发 + 回退脚本）

---

## 需求 3：维持模板资产可用与可发布

**用户故事**：作为模板维护者，我希望模板库持续可用，避免“模板过期或不可发现”。

### 验收标准

3.1 新增/更新模板后可通过 `kse templates search/show` 验证  
3.2 模板仓库与项目导出目录保持一致性  
3.3 模板发布证据持续回填到 handoff 包  
3.4 官方源可发现性异常时，给出回退与恢复步骤

---

## 需求 4：主从协作与上下文自动续航

**用户故事**：作为多 agent 协作者，我希望会话中断后可无缝续跑。

### 验收标准

4.1 每轮更新 `.kiro/steering/CURRENT_CONTEXT.md`（版本递增）  
4.2 每轮更新 `docs/handoffs/handoff-manifest.json` 与 `open-risks.md`  
4.3 编排统一通过 `kse-wave-runner.ps1` 低并发回退模式执行  
4.4 任何子 Spec 都可通过文档与证据独立复盘
