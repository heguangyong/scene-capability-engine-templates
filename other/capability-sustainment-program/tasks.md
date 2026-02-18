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

# 任务清单：能力持续闭环总控（63-00）

- [x] 1 建立 63 系列主从图谱
  - [x] 1.1 创建并校验 `63-01~63-04` 文档骨架
  - [x] 1.2 约定每轮输入输出与证据口径
  - [x] 1.3 设定统一批次命名与日志落盘规则

- [x] 2 建立持续能力回填机制
  - [x] 2.1 定义“变更→矩阵回填”最小流程
  - [x] 2.2 定义回退项 P0 响应机制
  - [x] 2.3 建立每轮统计快照更新规范

- [x] 3 建立模板持续发布机制
  - [x] 3.1 规范导出模板与仓库模板对账流程
  - [x] 3.2 固化 `templates update/search/show` 复核脚本
  - [x] 3.3 更新模板可发现性证据索引

- [x] 4 建立编排风险守护
  - [x] 4.1 强制采用低并发 + 回退策略
  - [x] 4.2 每轮记录 429 与回退情况
  - [x] 4.3 定期复核 `kse-wave-runner` 参数有效性

- [x] 5 治理收口
  - [x] 5.1 执行 `npx kse status --verbose`
  - [x] 5.2 执行 `npx kse doctor --docs`
  - [x] 5.3 回填 `.kiro/steering/CURRENT_CONTEXT.md`
  - [x] 5.4 回填 `docs/handoffs/handoff-manifest.json` 与 `open-risks.md`
