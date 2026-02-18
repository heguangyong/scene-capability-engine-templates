---
name: moqui-full-capability-closure-program
category: other
description: Template for Moqui Full Capability Closure Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：Moqui 全能力补齐与模板化总控（62-00）

## 任务列表

- [x] 1 升级执行基座
  - [x] 1.1 升级 `kiro-spec-engine` 到 `1.47.0`
  - [x] 1.2 校验 `kse --version` 输出 `1.47.0`
  - [x] 1.3 更新 `.kiro/steering/CURRENT_CONTEXT.md` 版本信息

- [x] 2 建立能力项级对齐矩阵
  - [x] 2.1 从 `01-00` 18 领域下钻到能力项清单
  - [x] 2.2 映射到现有后端 Controller/Service 与前端 Route/View
  - [x] 2.3 输出 `Full/Partial/Missing` 三态矩阵并附证据路径
  - [x] 2.4 产出 `docs/moqui-capability-itemized-matrix.md`

- [x] 3 形成 62 系列子 Spec 图谱
  - [x] 3.1 创建 `62-01`（能力项级对齐矩阵）
  - [x] 3.2 创建 `62-02`（业务深度缺口收敛）
  - [x] 3.3 创建 `62-03`（跨域端到端链路闭环）
  - [x] 3.4 创建 `62-04`（平台治理与运维能力闭环）
  - [x] 3.5 创建 `62-05`（331 评估缺口清零）
  - [x] 3.6 创建 `62-06`（Spec 模板产品化）

- [x] 4 推进缺口收敛（目标 1+2）
  - [x] 4.1 清零 P0/P1 缺口并提供验证证据
  - [x] 4.2 P2 缺口形成明确排期与验收标准
  - [x] 4.3 汇总最终能力对齐报告

- [x] 5 生成与验证模板（目标 3）
  - [x] 5.1 基于完成态 Spec 执行 `kse templates create-from-spec`
  - [x] 5.2 校验模板可被 `kse templates list/show` 识别
  - [x] 5.3 补齐模板使用文档（初始化/执行/回填）

- [x] 6 治理闭环
  - [x] 6.1 执行 `kse status --verbose`
  - [x] 6.2 执行 `kse doctor --docs`
  - [x] 6.3 更新 `.kiro/steering/CURRENT_CONTEXT.md`（版本递增）
