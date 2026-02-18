---
name: domain-depth-regression-guard
category: other
description: Template for Domain Depth Regression Guard
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：领域深度回归守护（63-01）

## 需求 1：维持领域能力深度不回退

### 验收标准

1.1 对新增需求进行领域归类并映射能力项 ID  
1.2 变更后更新 `62-01` 对应能力项状态与证据路径  
1.3 发现回退项时，同批次给出修复或明确阻断说明

## 需求 2：维持领域内契约一致性

### 验收标准

2.1 API 入参与出参契约在 controller/service/frontend 三层一致  
2.2 关键状态机（审批、关闭、回退）前后端定义一致  
2.3 领域文档与代码路径双向可追溯

## 需求 3：维持领域回归证据闭环

### 验收标准

3.1 每批至少补充一份领域回归证据文档  
3.2 证据包含命令、结果、时间戳与关键路径  
3.3 证据统一存放在 `docs/handoffs/evidence` 并被总控引用
