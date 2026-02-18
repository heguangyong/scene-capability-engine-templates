---
name: master-data-deepening
category: other
description: Template for Master Data Deepening
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：主数据深能力（60-01）

## 概述

在现有 Product/BOM/Routing 基础上补齐 Moqui 主数据深能力，优先覆盖多组织属性、变体 BOM、替代策略和协/副产品能力。

## 需求

### 需求 1：主数据模型增强
1.1 支持多组织（工厂/库位）维度属性  
1.2 支持批次与序列号策略配置  
1.3 支持状态控制（禁用/限制/报废）

### 需求 2：BOM 深能力
2.1 支持变体 BOM 与版本管理  
2.2 支持替代件优先级与比例  
2.3 支持协/副产品关系

### 需求 3：路由与主数据联动
3.1 支持物料-路由多版本绑定  
3.2 路由工序支持能力与时间标准字段

