---
name: global-search
category: other
description: Template for Global Search
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 全局搜索 — 任务清单

## 任务列表

- [x] 1. 后端 SearchService
  - [x] 1.1 创建 `SearchService.java`，实现跨模块搜索
  - [x] 1.2 实现模块配置映射（entity 名称、主键、搜索字段）
  - [x] 1.3 实现 `search(keyword, modules, maxPerModule)` 方法
  - [x] 1.4 每个模块用 like 条件匹配名称/编号，最多返回 5 条

- [x] 2. 后端 SearchController
  - [x] 2.1 创建 `SearchController.java`，注册 GET /api/v1/search 路由
  - [x] 2.2 在 `RestApplication.java` 中注册 Service 和 Controller

- [x] 3. 后端编译验证
  - [x] 3.1 运行 `cd backend && mvn clean package -DskipTests` 确认编译通过

- [x] 4. 前端类型和 Store
  - [x] 4.1 创建 `types/search.ts` 定义搜索结果接口
  - [x] 4.2 创建 `stores/search.ts` 实现搜索 API 调用和状态管理

- [x] 5. 前端搜索对话框
  - [x] 5.1 创建 `GlobalSearchDialog.vue` 搜索对话框组件
  - [x] 5.2 实现搜索输入框 + 300ms 防抖
  - [x] 5.3 实现结果按模块分组显示
  - [x] 5.4 实现键盘导航（↑↓ 选择，Enter 跳转，ESC 关闭）

- [x] 6. App.vue 集成
  - [x] 6.1 注册 Ctrl+K 全局快捷键
  - [x] 6.2 引入 GlobalSearchDialog 组件
  - [x] 6.3 实现搜索结果点击跳转

- [x] 7. 前端编译验证
  - [x] 7.1 运行 `cd frontend && npm run build` 确认编译通过
