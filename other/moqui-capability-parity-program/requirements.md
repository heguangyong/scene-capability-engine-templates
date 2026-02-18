---
name: moqui-capability-parity-program
category: other
description: Template for Moqui Capability Parity Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 能力对齐迁移总控（60-00）

## 概述

本 Spec 负责把“原 Moqui 业务能力”系统化迁移到当前 `331-poc` 新架构（Pure REST + Vue/Electron）中，先完成可审计的基线评估，再以 `spec + scene + 主从 agent` 方式批量推进。

---

## 需求 1：建立能力对齐基线（Assessment First）

**用户故事**：作为项目负责人，我需要一份可追踪的 Moqui 能力对齐矩阵，明确已实现、部分实现和缺口，避免后续并行开发偏航。

### 验收标准

1.1 输出原能力域清单（按 `01-00-customer-requirements` 的 18 个领域）  
1.2 输出新架构实现清单（后端 Controller/Service、前端路由/视图、API docs）  
1.3 形成“领域 → 现状 → 缺口 → 优先级(P0/P1/P2) → 目标 Spec”矩阵  
1.4 每一项判断必须附带证据路径（代码或文档）

---

## 需求 2：按 Scene 拆解迁移执行单元

**用户故事**：作为执行负责人，我需要把总目标拆成可独立运行的 Scene 合同，便于并行推进和质量守卫。

### 验收标准

2.1 为总控 Spec 提供 Scene manifest（`docs/scene.yaml`）并通过 validate  
2.2 Scene 合同包含：目标、输入边界、输出产物、风险级别、幂等/回滚策略  
2.3 Scene 需要可映射到子 Spec 批次（Wave/Batch）

---

## 需求 3：形成完整迁移 Spec 图谱

**用户故事**：作为架构 owner，我需要一个完整的 Spec 图谱（Master + 子 Spec），覆盖 Moqui 关键功能域。

### 验收标准

3.1 产出 Master Spec 与子 Spec 列表（包含编号、范围、依赖、优先级）  
3.2 子 Spec 覆盖核心业务域：主数据、销售、生产、采购库存、成本、设备、服务保障、项目、工装、平台能力  
3.3 定义跨 Spec 集成收敛点（导航、权限、API 契约、数据一致性）

---

## 需求 4：主从 Agent 并行执行策略

**用户故事**：作为交付经理，我需要稳定的主从并行策略，在不触发频繁中断和 429 限流的前提下持续推进。

### 验收标准

4.1 明确主 Agent 职责（编排、收敛、集成、上下文维护）  
4.2 明确从 Agent 职责（按子 Spec 实现、提交证据、通过门禁）  
4.3 明确并发策略（默认 `maxParallel=1`，按批次逐步提并发）与限流回退机制  
4.4 每轮批次完成后自动更新 `.kiro/steering/CURRENT_CONTEXT.md`

---

## 需求 5：端到端验收与治理闭环

**用户故事**：作为质量负责人，我需要每轮迁移有统一验收口径，确保“完成”可验证、可复盘、可交接。

### 验收标准

5.1 每个子 Spec 必须有 requirements/design/tasks 与验证证据  
5.2 总控 Spec 提供批次验收清单（构建、测试、接口、文档、上下文）  
5.3 `kse doctor --docs` 与 `kse status --verbose` 持续可通过  
5.4 形成最终能力对齐报告（Moqui 原能力 vs 新架构落地能力）
