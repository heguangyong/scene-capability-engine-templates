---
name: phase2-quality-debt-closure
category: other
description: Template for Phase2 Quality Debt Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: Phase 2 质量债收敛（12-00）

## 设计目标

用最小侵入方式建立“可重复质量扫描 → 结构化报告 → 收敛波次计划”三段式流程，优先保证可追踪与可执行。

## 核心方案

1. **统一扫描脚本**
   - 新增 `scripts/quality-baseline.ps1`，统一触发：
     - `framework` compile/test
     - `app` test
     - JaCoCo 报告生成（app）
     - CheckStyle / PMD / SpotBugs（framework）
   - 输出机器可读中间结果（XML/CSV）与 Markdown 汇总。

2. **报告落盘规范**
   - 当前 Spec 下 `reports/` 固定命名：
     - `QUALITY_BASELINE_<date>.md`
     - `QUALITY_WAVE1_PLAN_<date>.md`
   - 汇总报告包含：
     - 覆盖率汇总（instruction/branch）
     - 覆盖率缺口 Top10 类
     - CheckStyle/PMD/SpotBugs 总量
     - SpotBugs 严重度分布 + 热点 Top10 类

3. **Wave 计划分批策略**
   - Wave 1：只聚焦高价值 API 路径（app service/controller 热点类）
   - Wave 2：聚焦 framework 高热点核心类（优先 P1/P2 SpotBugs）
   - Wave 3：长尾问题清理 + 门槛拉升

## 非目标（本轮不做）

- 不在本轮直接重构大量 framework 代码。
- 不在本轮强行把覆盖率拉到 70%。
- 不在本轮引入新的测试框架或重型基建改造。

## 验证闭环

1. 执行质量基线脚本并成功产出报告。
2. `kse status` 可看到本 Spec 任务推进。
3. `kse docs validate --all` 保持合规。

