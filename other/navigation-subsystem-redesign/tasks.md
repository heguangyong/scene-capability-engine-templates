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

# Tasks: 42-00 {{SPEC_NAME_TITLE}}

## 任务列表

- [x] 1 定义子系统分组导航模型
  - [x] 1.1 扩展 `NavItem` 支持 `children`
  - [x] 1.2 定义模块到子系统映射关系

- [x] 2 重构侧边栏渲染逻辑
  - [x] 2.1 仅展示子系统级入口
  - [x] 2.2 保持激活态与路由状态同步

- [x] 3 实现子系统上下文面板
  - [x] 3.1 新增 `SubsystemPanel` 模块列表展示
  - [x] 3.2 点击模块后切换到对应业务页面

- [x] 4 完成联调与验证
  - [x] 4.1 验证二级切换与高亮一致性
  - [x] 4.2 验证深链路由回显一致性
