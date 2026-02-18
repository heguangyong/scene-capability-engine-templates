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

# 需求文档：能力项级对齐矩阵（62-01）

## 需求 1：建立能力项清单
### 验收标准
1.1 基于 18 领域下钻为能力项级目录  
1.2 每项具备唯一 ID、领域、子域、描述  
1.3 能力项总表可导出

## 需求 2：建立证据映射
### 验收标准
2.1 每能力项映射后端/前端/API 证据路径  
2.2 状态三态：`Full/Partial/Missing`  
2.3 每个 `Partial/Missing` 有缺口说明

## 需求 3：输出闭环输入
### 验收标准
3.1 导出对齐矩阵文档  
3.2 生成缺口台账供 62-02~62-05 消化  
3.3 CURRENT_CONTEXT 记录统计结果
