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

# 任务清单：Moqui 能力对齐迁移总控（60-00）

## 任务列表

- [x] 1 建立能力基线评估
  - [x] 1.1 盘点原需求 18 领域能力清单
  - [x] 1.2 盘点新架构已实现能力（后端/前端/API）
  - [x] 1.3 输出能力对齐基线文档 `docs/moqui-capability-baseline-{{DATE}}.md`

- [x] 2 建立总控 Spec 与 Scene 合同
  - [x] 2.1 编写 `requirements.md`
  - [x] 2.2 编写 `design.md`
  - [x] 2.3 生成并调整 `docs/scene.yaml`
  - [x] 2.4 执行 `kse scene validate` 并归档结果

- [x] 3 形成完整子 Spec 图谱（Wave A/B/C）
  - [x] 3.1 创建 Wave A 子 Spec（60-01 ~ 60-05）
  - [x] 3.2 创建 Wave B 子 Spec（60-06 ~ 60-09）
  - [x] 3.3 创建 Wave C 子 Spec（60-10 ~ 60-12）
  - [x] 3.4 为每个子 Spec 生成 scene manifest

- [x] 4 设计主从 Agent 并行推进机制
  - [x] 4.1 定义 `kse collab` 主从分工和接口契约
  - [x] 4.2 定义 `kse orchestrate` 分批次执行策略
  - [x] 4.3 定义限流与回退机制（429 自动降并发）

- [x] 5 治理闭环与上下文同步
  - [x] 5.1 执行 `kse doctor --docs`
  - [x] 5.2 执行 `kse status --verbose`
  - [x] 5.3 更新 `.kiro/steering/CURRENT_CONTEXT.md`
