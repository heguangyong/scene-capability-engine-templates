---
name: moqui-suite-final-alignment-program
category: other
description: Template for Moqui Suite Final Alignment Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：Moqui 三套件最终对齐收官计划（65-00）

- [x] 1 产出三套件最终缺口审计
  - [x] 1.1 形成 P0/P1/P2 缺口清单（含证据路径）
  - [x] 1.2 形成套件到模块/路由/规则对照矩阵
  - [x] 1.3 输出审计结论与执行优先级

- [x] 2 建立主从 spec 并行执行图谱
  - [x] 2.1 创建 `65-01/65-02/65-03` 子 spec
  - [x] 2.2 定义子 spec 分工边界与交付契约
  - [x] 2.3 定义 `65-00` 总控收口流程

- [x] 3 固化多 agent 批跑与治理 gate
  - [x] 3.1 新增 `scripts/kse-suite65-wave.ps1`
  - [x] 3.2 固化 `status/doctor/final-sync` 收口链路
  - [x] 3.3 固化 ontology publish gate（70/100）并落证据

- [x] 4 收官模板化与交付同步
  - [x] 4.1 子 spec + 总控 spec 形成模板导出输入
  - [x] 4.2 更新 `CURRENT_CONTEXT` 与 handoff 清单
  - [x] 4.3 输出多 agent 执行手册
