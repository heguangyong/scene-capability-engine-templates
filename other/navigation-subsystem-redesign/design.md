---
name: navigation-subsystem-redesign
category: other
description: Template for Navigation Subsystem Redesign
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 导航子系统重构 — 设计文档

## 架构概述

该模板用于重构现有导航子系统，目标是统一菜单层级、路由映射、状态管理与交互语义。

## 核心设计点

1. 导航配置统一单一数据源（菜单、图标、路由元数据）。
2. 一级/二级菜单通过同一状态机驱动激活态与高亮态。
3. 菜单点击仅切换当前导航上下文，不回退旧页面栈。
4. 支持按域/模块拆分导航面板并复用共享渲染组件。

## 状态与路由

- 使用集中式 store 持久化：
  - `activeTopNav`
  - `activeSubNav`
  - `activeModule`
- 路由 query 与 store 双向同步，支持刷新回放。

## 验证要点

1. 二级菜单切换应保持在当前二级列表上下文。
2. 一级菜单点击后必须有清晰激活态。
3. 桌面与移动端菜单语义保持一致。
4. 新旧导航入口映射无重复维护点。
