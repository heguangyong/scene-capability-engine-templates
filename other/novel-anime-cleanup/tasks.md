---
name: novel-anime-cleanup
category: other
description: Template for Novel Anime Cleanup
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: Novel-Anime Business Code Cleanup

## Overview

按依赖关系从后端到前端、从外层到内层的顺序，分阶段移除所有小说转动漫业务代码。每个阶段完成后运行测试验证。

## Tasks

- [x] 1. 后端清理：移除 Novel-Anime 控制器和服务
  - [x] 1.1 删除后端 Novel-Anime 服务类文件
    - 删除 `backend/app/src/main/java/org/moqui/rest/service/` 下的: NovelService.java, CharacterService.java, SceneService.java, EpisodeService.java, ProjectService.java, CreditsService.java
    - _Requirements: 8.2_
  - [x] 1.2 删除后端 Novel-Anime 控制器文件
    - 删除 `backend/app/src/main/java/org/moqui/rest/controller/` 下的: NovelController.java, CharacterController.java, SceneController.java, EpisodeController.java, ProjectController.java, CreditsController.java, WorkflowController.java, AssetController.java
    - _Requirements: 8.1_
  - [x] 1.3 更新 RestApplication.java 移除已删除组件的注册
    - 移除 NovelService, CharacterService, SceneService, EpisodeService, ProjectService, CreditsService 的实例化
    - 移除 NovelController, CharacterController, SceneController, EpisodeController, ProjectController, CreditsController, WorkflowController, AssetController 的注册
    - _Requirements: 8.3_
  - [x] 1.4 更新 EndpointRegressionPropertyTest.java
    - 移除对已删除 Controller 的 import 和 register 调用
    - 从 endpointPaths() 提供者中移除 novel-anime 相关端点路径（/api/v1/novels/*, /api/v1/characters/*, /api/v1/scenes/*, /api/v1/episodes/*, /api/v1/projects, /api/v1/workflows, /api/v1/assets, /api/v1/credits/*）
    - _Requirements: 9.3_

- [x] 2. 后端验证检查点
  - 运行 `mvn test` (backend/) 确保所有保留测试通过
  - 确认编译无错误
  - Ensure all tests pass, ask the user if questions arise.
  - _Requirements: 9.1, 9.2_

- [x] 3. 前端清理 Phase 1：移除视图和组件
  - [x] 3.1 删除 Novel-Anime 视图文件
    - 删除: WorkflowEditor.vue, CharactersView.vue, CharacterDetailView.vue, AssetsView.vue, GeneratedContentView.vue, PreviewView.vue
    - 删除整个 `views/dashboard/` 目录
    - _Requirements: 1.1, 1.2_
  - [x] 3.2 创建简化的 DashboardView.vue 占位页面
    - 替换原有的 DashboardView.vue，显示欢迎信息（如 "331-POC ERP Platform"）和基本系统状态
    - 只依赖保留的 Store（auth, user, ui）
    - _Requirements: 1.3_
  - [x] 3.3 删除 Novel-Anime 组件目录
    - 删除以下整个目录: `components/novel/`, `components/character/`, `components/episode/`, `components/scene/`, `components/pipeline/`, `components/assets/`, `components/workflow/`, `components/progress/`, `components/welcome/`, `components/tutorial/`, `components/explorer/`
    - 删除 `components/README.md`
    - _Requirements: 2.1_

- [x] 4. 前端清理 Phase 2：移除服务和 API
  - [x] 4.1 删除 Novel-Anime 业务服务文件
    - 删除: AIService.ts, AIServiceTypes.ts, AIVideoGenerator.ts, AssetLibrary.ts, AssetSearchEngine.ts, AutoSaveManager.ts, BackupManager.ts, CharacterSystem.ts, EpisodeGenerator.ts, ExportService.ts, FileSystemService.ts, ImageGenerationService.ts, KeyStorageService.ts, NovelParser.ts, PipelineOrchestrator.js, PlotAnalyzer.ts, ProjectManager.js, ProjectManager.ts, ScriptConverter.ts, StoryboardCreator.ts, workflowService.ts
    - _Requirements: 3.1_
  - [x] 4.2 删除 Novel-Anime API 服务文件
    - 删除: novelApi.ts, episodeApi.ts, sceneApi.ts, characterApi.ts, pipelineApi.ts, creditsApi.ts
    - _Requirements: 3.2_
  - [x] 4.3 删除服务 README 文件
    - 删除: README_AIService.md, README_AIVideoGenerator.md, README_AssetLibrary.md, README_BackupManager.md, README_EpisodeGenerator.md, README_PlotAnalyzer.md, README_ScriptConverter.md, README_StoryboardCreator.md, README_VisualElementCaching.md, README_WorkflowEditor.md, README.md
    - _Requirements: 3.3_
  - [x] 4.4 更新 services/index.ts 只导出核心服务
    - 移除 novelApi, pipelineApi, characterApi, sceneApi, episodeApi 的导出
    - 保留: apiService, apiV1Client, entityService, serviceInvoker, authServiceV1, tokenManager, apiDiscoveryService
    - _Requirements: 3.4_

- [x] 5. 前端清理 Phase 3：移除 Store、Composable、类型和工具
  - [x] 5.1 删除 Novel-Anime Store 文件
    - 删除: project.ts, file.js, novel.js, tab.js, task.js, session.ts, command.ts, credits.ts, workflowStore.ts
    - 删除 `stores/README.md`
    - _Requirements: 4.1_
  - [x] 5.2 简化 Navigation Store
    - 移除 workflow, assets, characters 相关的 NAV_ITEMS 条目
    - 移除 WorkflowContext, AssetsContext, CharactersContext 类型定义
    - 简化 PanelContext 和 DEFAULT_CONTEXTS 只保留 dashboard + settings
    - 更新 NavId 类型为 'dashboard' | 'settings'
    - 更新 currentPanelComponent getter 的 componentMap
    - _Requirements: 4.3_
  - [x] 5.3 删除 Novel-Anime Composable 文件
    - 删除: useCreditsCheck.ts, useTutorial.ts, useVirtualList.ts, useLazyLoad.ts
    - 更新 composables/index.ts 只导出 useAppInit（移除 useCreditsCheck 相关导出）
    - _Requirements: 5.1, 5.7_
  - [x] 5.4 删除 Novel-Anime 类型和工具文件
    - 删除类型: types/project.ts, types/workflow.ts
    - 更新 types/index.ts 只导出 api 和 apiV1
    - 删除工具: encryption.ts, memoryManager.ts, performance.ts, versionManager.js, SessionManager.ts
    - _Requirements: 5.3, 5.4, 5.5_

- [x] 6. 前端清理 Phase 4：简化路由和 App.vue
  - [x] 6.1 简化 router/index.js
    - 移除 WorkflowEditor, AssetsView, CharactersView, CharacterDetailView, PreviewView, GeneratedContentView 的导入和路由定义
    - 保留路由: /login, / (redirect), /dashboard, /settings, /profile
    - _Requirements: 6.1, 6.2_
  - [x] 6.2 简化 App.vue
    - 移除对已删除 Store 的导入: useProjectStore, useTaskStore, useFileStore
    - 移除对已删除 Composable 的导入: useTutorial
    - 移除 TutorialOverlay 组件导入和使用
    - 移除 versionManager 导入和使用
    - 简化 navItems 数组只保留 dashboard（移除 workflow, assets, characters）
    - 移除 createProject, openProject, saveProject, generateAnimation 函数
    - 移除 Electron menu action 中的 novel-anime 相关处理
    - 将侧边栏标签 "NOVEL ANIME" 改为 "ERP"
    - 移除 mainAreaTitle/mainAreaSubtitle 中的小说动漫相关文案
    - _Requirements: 6.3, 6.4, 6.5, 6.6, 6.7_

- [x] 7. 前端清理 Phase 5：移除测试文件
  - [x] 7.1 删除 Novel-Anime 测试文件
    - 删除: services/__tests__/AIService.test.ts, services/__tests__/BackupManager.test.ts, services/__tests__/domainMigration.test.ts, services/errorHandler.spec.ts, services/workflowService.spec.ts, stores/__tests__/workflowStore.checkpoint.test.ts, stores/workflowStore.spec.ts
    - _Requirements: 7.1_

- [x] 8. 前端验证检查点
  - 运行 `npm test` (frontend/) 确保所有保留测试通过
  - 确认无 TypeScript 编译错误
  - Ensure all tests pass, ask the user if questions arise.
  - _Requirements: 7.3, 9.1_

- [x] 9. 文档和 Steering 更新
  - [x] 9.1 更新 ENVIRONMENT.md
    - 更新测试计数为清理后的实际数量
    - _Requirements: 10.1_
  - [x] 9.2 更新 CURRENT_CONTEXT.md
    - 添加 21-00 Spec 完成状态
    - 更新架构描述反映清理后状态
    - 更新测试计数
    - _Requirements: 10.2_

- [x] 10. 最终验证检查点
  - 运行 `mvn test` (backend/) 和 `npm test` (frontend/) 全量验证
  - 确认所有测试通过
  - Ensure all tests pass, ask the user if questions arise.
  - _Requirements: 7.3, 9.1_

## Notes

- 后端先于前端清理，因为前端 API 服务依赖后端端点
- 每个 Phase 内的子任务按依赖关系排序
- 检查点任务确保增量验证，避免问题累积
- 本 Spec 无可选测试任务（`*`），因为不引入新功能代码
- 所有验证依赖现有测试套件，不需要编写新测试
