---
name: moqui-scene-operability-hardening-program
category: other
description: Template for Moqui Scene Operability Hardening Program
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Moqui 场景可操作性加固计划（67-00）

## 概述

本 Spec 承接 `66-00~66-03` 收官后遗留的 P1/P2 项，目标是补齐“可操作、可追溯、可治理”的最后一段闭环：

1. SuiteHub 增加场景成熟度与风险趋势视图；
2. 场景页统一网络异常语义与恢复动作；
3. 从 UI 直达 Spec/Evidence 证据路径，减少治理追溯成本。

## 需求 1：场景成熟度与风险趋势可视化

### 验收标准

1.1 SuiteHub 可展示按套件聚合的成熟度分值  
1.2 SuiteHub 可展示风险等级与待补项计数  
1.3 趋势信息可用于判断下一轮优先补齐方向

## 需求 2：网络异常语义统一

### 验收标准

2.1 离线/超时/限流/5xx 提示语义统一  
2.2 每类异常展示一致的恢复动作  
2.3 场景页可见“回退建议 + 重试建议”

## 需求 3：场景证据追溯入口

### 验收标准

3.1 SuiteHub 可直接查看各套件 Spec/Evidence 路径  
3.2 路径可一键复制以支持交接复核  
3.3 追溯信息与 `CURRENT_CONTEXT`、handoff 资产一致

## 需求 4：治理收口与模板资产同步

### 验收标准

4.1 `67-00~67-03` 完成态并通过 `status/doctor/final-sync`  
4.2 产出 scene/package/template/ontology 证据  
4.3 `CURRENT_CONTEXT` 与模板清单同步更新

