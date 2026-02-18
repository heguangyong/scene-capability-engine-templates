---
name: suite-hub-ui-integration
category: other
description: Template for Suite Hub Ui Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：三大应用入口页集成（64-04）

## 概述

在现有领域化导航基础上，新增“三大应用视角入口页”（Suite Hub），为 HiveMind / PopCommerce / MarbleERP 提供聚合导航，提升跨域业务认知效率。

## 需求 1：入口页能力

### 验收标准

1.1 提供独立路由 `/suite-hub`  
1.2 页面按三大应用展示模块入口与跨套件关键入口  
1.3 入口点击可跳转到既有模块页面

## 需求 2：导航集成

### 验收标准

2.1 一级菜单新增“应用视图”入口  
2.2 导航状态高亮与路由保持一致  
2.3 移动端与桌面端共用同一导航数据源

## 需求 3：治理闭环

### 验收标准

3.1 前端构建通过  
3.2 `kse status --verbose` 与 `kse doctor --docs` 通过  
3.3 产物纳入 handoff 索引

