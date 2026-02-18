---
name: wave6-integration
category: other
description: Template for Wave6 Integration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 需求文档：Wave 6 多模块导航集成

## 概述

将 Wave 6 的 3 个业务模块（人力资源管理、退货管理、工作日历）集成到前端导航系统中。由于 3 个模块由 Codex 子 Agent 并行开发，各自修改了共享文件（navigation.ts、ContextPanel.vue、App.vue、router/index.js、RestApplication.java），需要统一合并。

## 背景

Wave 6 的 3 个模块（Spec 50-00 ~ 52-00）由 Agent Orchestrator 并行执行完成。每个 Agent 独立修改了共享文件，需要主 Agent 统一集成：
- 50-00: 人力资源管理（新增 hr 子系统：employee + department）
- 51-00: 退货管理（扩展 sales 子系统：return-rma）
- 52-00: 工作日历（扩展 mfg 子系统：work-calendar）

## 验收标准

### 1. 后端集成

- 1.1 RestApplication.java 注册 HrService + HrController
- 1.2 RestApplication.java 注册 ReturnService + ReturnController
- 1.3 RestApplication.java 注册 CalendarService + CalendarController
- 1.4 后端 `mvn clean package -DskipTests` 编译通过

### 2. 导航状态 Store 集成

- 2.1 SubsystemId 添加 'hr'
- 2.2 ModuleId 添加 'employee' | 'department' | 'return-rma' | 'work-calendar'
- 2.3 SUBSYSTEM_MODULE_MAP 添加 hr 子系统，扩展 sales 和 mfg
- 2.4 MODULE_SUBSYSTEM_MAP 添加 4 个模块映射
- 2.5 PanelContext 和 DEFAULT_CONTEXTS 添加 5 个条目（hr + 4 模块）
- 2.6 componentMap 添加 5 个面板映射

### 3. 面板组件注册

- 3.1 ContextPanel.vue 注册 HrPanel、ReturnPanel、CalendarPanel
- 3.2 panelComponentMap 添加 4 个模块映射
- 3.3 subsystemConfigs 添加 hr 子系统，扩展 sales 和 mfg

### 4. 侧边栏导航

- 4.1 App.vue 添加 hr 子系统导航（employee + department）
- 4.2 App.vue 的 sales 子系统添加 return-rma
- 4.3 App.vue 的 mfg 子系统添加 work-calendar

### 5. 路由配置

- 5.1 追加 employee 列表和详情路由
- 5.2 追加 department 列表和详情路由
- 5.3 追加 return-rma 列表和详情路由
- 5.4 追加 work-calendar 列表和详情路由
- 5.5 追加 shift 列表路由
- 5.6 所有路由设置 meta: { requiresAuth: true }

### 6. 编译验证

- 6.1 前端 `npm run build` 编译通过
- 6.2 后端 `mvn clean package -DskipTests` 编译通过
