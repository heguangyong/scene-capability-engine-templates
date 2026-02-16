---
name: moqui-full-capability-closure-program
category: backend-features
description: Template for Moqui Full Capability Closure Program
tags: [moqui, full, capability, closure, orchestration]
author: FallingAKS
created_at: '2026-02-16'
updated_at: '2026-02-16'
version: 1.0.0
kse_version: 1.47.7
---

# 需求文档：Moqui 全能力补齐与模板化总控（62-00）

## 概述

本 Spec 用于承接三项目标并作为后续主从并行执行的唯一总控入口：

1. 完全补齐原 Moqui 功能（以可验证能力对齐为准）
2. 补齐 `331-poc` 评估中缺失功能点（历史 P0/P1/P2 缺口清零）
3. 将补齐后的完整能力沉淀为可复用 Spec Template

---

## 需求 1：完成 Moqui 原能力对齐闭环

**用户故事**：作为项目 owner，我需要一份“原能力到现能力”的逐项闭环结果，确保不再停留在“部分实现”。

### 验收标准

1.1 以 `01-00` 的 18 领域作为对齐主维度，建立“能力项级”映射清单  
1.2 每个能力项必须标记 `Full / Partial / Missing` 三态，并附证据路径  
1.3 `Partial / Missing` 项必须绑定目标子 Spec、负责人和验收测试  
1.4 收敛后输出最终“原 Moqui vs 新架构”闭环报告

---

## 需求 2：补齐 331-poc 评估缺失项

**用户故事**：作为交付负责人，我需要把历史评估中的缺失项全部落地，避免“文档完成但能力未闭环”。

### 验收标准

2.1 汇总 `60-00` 与 `61-00` 的剩余缺口，形成统一 backlog  
2.2 P0/P1 缺口在本轮收敛为 `0`；P2 需进入明确排期并具备验收口径  
2.3 每项补齐必须包含 API/前端/文档三类至少两类证据  
2.4 通过 `kse status --verbose` 显示任务完成且治理合规

---

## 需求 3：形成可复用 Spec Template

**用户故事**：作为后续并行执行者，我需要可直接复用的模板，快速复制“评估→拆解→实现→验收→沉淀”的流程。

### 验收标准

3.1 输出“能力补齐型 Spec 模板”（requirements/design/tasks/docs 结构）  
3.2 模板内置：能力矩阵、差距分级、Scene 合同、主从编排、DoD 清单  
3.3 通过 `kse templates create-from-spec` 生成模板产物并可被 `kse templates list/show` 识别  
3.4 给出模板使用说明（如何初始化、如何批量执行、如何回填上下文）

---

## 需求 4：主从 Agent 无缝推进机制

**用户故事**：作为协作者，我需要在会话中断/切换时仍能稳定接续推进，不丢上下文。

### 验收标准

4.1 保持 `CURRENT_CONTEXT.md` 每轮自动更新（版本递增 + 关键变更）  
4.2 明确 `Master/Sub` 的输入输出契约和锁机制（防冲突）  
4.3 编排默认串行，稳定后分批提并发，429 自动降并发回退  
4.4 每个子 Spec 都可独立复盘（目标、变更、验证、风险、下一步）
