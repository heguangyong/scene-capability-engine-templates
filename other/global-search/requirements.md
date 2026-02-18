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

# 全局搜索 — 需求文档

## 概述

新增全局搜索功能，支持 Cmd+K（Mac）/ Ctrl+K（Windows）快捷键唤起搜索对话框，跨模块搜索产品、订单、Party、发票等业务数据。纯前端实现。

---

## 用户故事

### US-1: 快捷键唤起搜索
作为用户，我需要通过键盘快捷键快速打开搜索对话框。

**验收标准:**
- AC-1.1: Ctrl+K 打开搜索对话框
- AC-1.2: ESC 关闭搜索对话框
- AC-1.3: 对话框居中显示，带半透明遮罩
- AC-1.4: 打开时自动聚焦搜索输入框

### US-2: 跨模块搜索
作为用户，我需要在一个搜索框中搜索所有业务模块的数据。

**验收标准:**
- AC-2.1: 支持搜索以下模块：产品、订单、Party、发票、采购、库存、BOM、工艺路线、生产工单、设施、发货
- AC-2.2: 搜索结果按模块分组显示
- AC-2.3: 每个结果显示：名称、模块标签、简要描述
- AC-2.4: 输入防抖 300ms，避免频繁请求
- AC-2.5: 空输入时显示最近访问或热门模块入口

### US-3: 搜索结果导航
作为用户，我需要点击搜索结果直接跳转到对应页面。

**验收标准:**
- AC-3.1: 点击结果跳转到对应模块的详情页
- AC-3.2: 支持键盘上下箭头选择结果
- AC-3.3: Enter 键确认跳转
- AC-3.4: 跳转后自动关闭搜索对话框

### US-4: 搜索 API
作为前端应用，我需要后端提供统一的搜索 API。

**验收标准:**
- AC-4.1: `GET /api/v1/search?q={keyword}&modules={module1,module2}` — 全局搜索
- AC-4.2: 返回结果按模块分组，每组最多 5 条
- AC-4.3: 支持 modules 参数限定搜索范围（可选，默认搜索全部）
- AC-4.4: 关键词匹配名称、编号、描述等字段

---

## 非功能需求

- NFR-1: 搜索响应时间 < 500ms
- NFR-2: 遵循现有代码模式
- NFR-3: 后端使用 Moqui entity 查询
- NFR-4: 前端使用 Vue 3 + TypeScript
- NFR-5: 编译验证：后端 `mvn clean package -DskipTests`，前端 `npm run build`
