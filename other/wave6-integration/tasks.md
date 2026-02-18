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

# 任务清单：53-00 Wave 6 集成

## 概述

合并 3 个 Codex Agent 并行开发的模块到共享文件中。

## 任务列表

- [x] 1. 后端集成
  - [x] 1.1 修改 RestApplication.java 注册 HrService/HrController、ReturnService/ReturnController、CalendarService/CalendarController
  - [x] 1.2 运行 mvn clean package -DskipTests 确认后端编译通过

- [x] 2. 导航 Store 集成
  - [x] 2.1 修改 navigation.ts：SubsystemId 添加 hr，ModuleId 添加 4 个模块
  - [x] 2.2 修改 navigation.ts：SUBSYSTEM_MODULE_MAP 和 MODULE_SUBSYSTEM_MAP 添加映射
  - [x] 2.3 修改 navigation.ts：PanelContext、DEFAULT_CONTEXTS、componentMap 添加条目

- [x] 3. 面板和导航集成
  - [x] 3.1 修改 ContextPanel.vue：注册 HrPanel、ReturnPanel、CalendarPanel，添加 panelComponentMap 和 subsystemConfigs
  - [x] 3.2 修改 App.vue：添加 hr 子系统，扩展 sales 和 mfg 子系统的 children
  - [x] 3.3 修改 router/index.js：添加 9 条路由
  - [x] 3.4 修改 icons.js：确保所需图标已导入

- [x] 4. 编译验证
  - [x] 4.1 运行 npm run build 确认前端编译通过
  - [x] 4.2 运行 mvn clean package -DskipTests 确认后端编译通过
