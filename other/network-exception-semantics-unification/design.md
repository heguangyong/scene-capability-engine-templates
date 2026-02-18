---
name: network-exception-semantics-unification
category: other
description: Template for Network Exception Semantics Unification
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：网络异常语义统一（67-02）

## 范围

- `frontend/src/renderer/services/apiNetworkMonitor.ts`
- `frontend/src/renderer/services/apiV1Client.ts`
- `frontend/src/renderer/components/scene/SceneBlueprintBanner.vue`

## 关键设计

1. 网络状态扩展为 `online/offline/degraded`；  
2. 对 `timeout` 与连接失败标记 `offline`；  
3. 对 `429` 与 `5xx` 标记 `degraded`；  
4. SceneBanner 统一展示“恢复动作 + 回退建议”块。  

## 语义映射

- `offline`: 后端不可达、DNS/连接失败、超时
- `degraded`: 429 限流、5xx 服务异常
- `online`: 请求成功或非网络类业务错误

## 验证

- `npm run build`（frontend）
- 手动触发离线/限流路径验证 SceneBanner 文案

