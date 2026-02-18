---
name: moqui-capability-itemized-parity-matrix
category: other
description: Template for Moqui Capability Itemized Parity Matrix
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：能力项级对齐矩阵（62-01）

- [x] 1 建立能力项数据模型
  - [x] 1.1 定义字段与状态枚举
  - [x] 1.2 建立能力项 ID 规则

- [x] 2 采集证据路径
  - [x] 2.1 采集后端控制器/服务路径
  - [x] 2.2 采集前端路由/视图路径
  - [x] 2.3 采集 API docs 路径

- [x] 3 形成矩阵与缺口台账
  - [x] 3.1 输出能力矩阵文档
  - [x] 3.2 输出缺口 backlog 文档
  - [x] 3.3 回填 CURRENT_CONTEXT

- [x] 4 治理验证
  - [x] 4.1 执行 `kse status --verbose`
  - [x] 4.2 执行 `kse doctor --docs`
