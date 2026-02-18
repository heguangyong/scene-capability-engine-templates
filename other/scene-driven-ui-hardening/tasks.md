---
name: scene-driven-ui-hardening
category: other
description: Template for Scene Driven Ui Hardening
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 任务清单：场景化 UI 补强与网络韧性（64-07）

- [x] 1 网络韧性补强
  - [x] 1.1 增加 API 网络状态监控服务
  - [x] 1.2 在 `apiV1Client` 增加幂等网络重试与统一断连提示
  - [x] 1.3 在主界面接入在线/离线状态展示

- [x] 2 场景模型与菜单补强
  - [x] 2.1 建立统一 `scene-blueprints` 配置
  - [x] 2.2 在 `SubsystemPanel` 增加模块场景摘要与要点
  - [x] 2.3 在 `PlatformPanel` 增加模块场景摘要与要点

- [x] 3 页面场景可视化与容错
  - [x] 3.1 新增 `SceneBlueprintBanner` 并挂载到主内容区
  - [x] 3.2 在 `SuiteHubView` 增加场景标签与回退基线
  - [x] 3.3 追加场景解析单测并执行前端构建验证
