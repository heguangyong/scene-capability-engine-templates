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

# Requirements Document

## Introduction

本 Spec 的目标是从 331-poc 项目的前端和后端中完全移除小说转动漫（Novel-Anime）业务功能代码。该业务依赖 AI 服务才能闭环，但目前没有可用的免费 AI 服务，价值主张不成立。清理后，前端将成为一个干净的 Vue 3 + Electron 外壳（含登录、个人资料、设置、空仪表盘占位、完整认证基础设施），后端保留所有 Moqui ERP 能力 + JWT/RBAC 安全 + REST API 基础设施。

## Glossary

- **Frontend_App**: Vue 3 + Electron 桌面应用前端（`frontend/`）
- **Backend_API**: Java 21 + Embedded Jetty REST API 后端（`backend/app/`）
- **Novel_Anime_Code**: 与小说转动漫业务相关的所有代码，包括视图、组件、服务、Store、API 端点、控制器、业务服务类
- **Core_Infrastructure**: 必须保留的基础设施代码，包括认证系统、API 客户端、路由基础设施、中间件管道、Moqui 框架
- **Router**: 前端 Vue Router 路由配置（`frontend/src/renderer/router/index.js`）
- **RestApplication**: 后端应用入口，负责注册所有控制器和服务（`RestApplication.java`）
- **Navigation_Store**: 前端导航状态管理（`stores/navigation.ts`）
- **Services_Index**: 前端服务统一导出文件（`services/index.ts`）
- **Preserved_Tests**: 清理后仍需通过的所有保留基础设施测试

## Requirements

### Requirement 1: 移除前端 Novel-Anime 视图

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的视图文件, so that 前端不再包含无法使用的业务页面。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下视图文件: DashboardView（小说工作流步骤）、WorkflowEditor、CharactersView、CharacterDetailView、AssetsView、GeneratedContentView、PreviewView
2. WHEN 清理完成, THE Frontend_App SHALL 不包含 `views/dashboard/` 目录下的所有子组件（ProjectInfo、ProjectList、ProjectOverview、ProjectProgress、QuickActions、WorkflowSteps）
3. WHEN 清理完成, THE Frontend_App SHALL 包含一个新的简化 DashboardView 作为占位页面，显示欢迎信息和系统状态
4. WHEN 清理完成, THE Frontend_App SHALL 保留 LoginView、ProfileView、Settings 视图不受影响

### Requirement 2: 移除前端 Novel-Anime 组件

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的组件目录, so that 前端组件树干净且无死代码。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下组件目录: `components/novel/`、`components/character/`、`components/episode/`、`components/scene/`、`components/pipeline/`、`components/assets/`、`components/workflow/`、`components/progress/`、`components/welcome/`、`components/tutorial/`、`components/explorer/`
2. WHEN 清理完成, THE Frontend_App SHALL 保留以下组件目录和文件: `components/layout/`、`components/panels/ContextPanel.vue`、`components/dialogs/ErrorDialog.vue`、`components/ui/MobileNav.vue`、`components/status/`

### Requirement 3: 移除前端 Novel-Anime 服务

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的服务文件, so that 前端服务层只保留核心基础设施。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下业务服务文件: AIService、AIServiceTypes、AIVideoGenerator、AssetLibrary、AssetSearchEngine、AutoSaveManager、BackupManager、CharacterSystem、EpisodeGenerator、ExportService、FileSystemService、ImageGenerationService、KeyStorageService、NovelParser、PipelineOrchestrator、PlotAnalyzer、ProjectManager（.js 和 .ts）、ScriptConverter、StoryboardCreator、workflowService
2. WHEN 清理完成, THE Frontend_App SHALL 不包含以下 API 服务文件: novelApi、episodeApi、sceneApi、characterApi、pipelineApi、creditsApi
3. WHEN 清理完成, THE Frontend_App SHALL 不包含以下 README 文件: README_AIService、README_AIVideoGenerator、README_AssetLibrary、README_BackupManager、README_EpisodeGenerator、README_PlotAnalyzer、README_ScriptConverter、README_StoryboardCreator、README_VisualElementCaching、README_WorkflowEditor、services/README
4. WHEN 清理完成, THE Services_Index SHALL 只导出核心基础设施服务: apiService、apiV1Client、entityService、serviceInvoker、authServiceV1、tokenManager、apiDiscoveryService
5. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心服务: api.ts、apiV1Client.ts、apiDiscoveryService.ts、authApi.ts、authServiceV1.ts、entityService.ts、errorMapper.ts、errorHandler.ts、serviceInvoker.ts、tokenManager.ts

### Requirement 4: 移除前端 Novel-Anime Store

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的 Pinia Store, so that 状态管理层只保留核心功能。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下 Store 文件: project.ts、file.js、novel.js、tab.js、task.js、session.ts、command.ts、credits.ts、workflowStore.ts
2. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心 Store: auth.ts、user.ts、ui.js、layout.ts、notification.ts、settings.js
3. WHEN 清理完成, THE Navigation_Store SHALL 被简化为只包含 dashboard 和 settings 两个导航项，移除 workflow、assets、characters 相关的类型定义和上下文

### Requirement 5: 移除前端 Novel-Anime Composable、类型和工具

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的 Composable、类型定义和工具函数, so that 前端辅助代码层干净。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下 Composable: useCreditsCheck、useTutorial、useVirtualList、useLazyLoad
2. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心 Composable: useAppInit、useDebounce、useErrorReporting、useResponsive、useTheme
3. WHEN 清理完成, THE Frontend_App SHALL 不包含以下类型文件: types/project.ts、types/workflow.ts
4. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心类型文件: types/api.ts、types/apiV1.ts、types/index.ts（简化后）
5. WHEN 清理完成, THE Frontend_App SHALL 不包含以下工具文件: encryption.ts、memoryManager.ts、performance.ts、versionManager.js、SessionManager.ts
6. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心工具: authLogger.ts、errorMessages.ts、inputValidation.ts、icons.js
7. WHEN 清理完成, THE Frontend_App SHALL 更新 composables/index.ts 只导出保留的 Composable

### Requirement 6: 简化前端路由和主应用

**User Story:** As a 开发者, I want to 简化路由配置和 App.vue, so that 前端只包含认证 + 设置 + 个人资料 + 占位仪表盘的路由。

#### Acceptance Criteria

1. WHEN 清理完成, THE Router SHALL 只包含以下路由: `/login`、`/dashboard`、`/settings`、`/profile`，以及 `/` 重定向到 `/dashboard`
2. WHEN 清理完成, THE Router SHALL 不包含以下路由: `/workflow`、`/generated`、`/preview`、`/assets`、`/characters`、`/characters/:id`
3. WHEN 清理完成, THE Frontend_App 的 App.vue SHALL 移除所有小说动漫相关的侧边栏导航项（workflow、assets、characters）
4. WHEN 清理完成, THE Frontend_App 的 App.vue SHALL 移除对已删除 Store（project、task、file）和 Composable（useTutorial）的导入和引用
5. WHEN 清理完成, THE Frontend_App 的 App.vue SHALL 移除 TutorialOverlay 组件引用
6. WHEN 清理完成, THE Frontend_App 的 App.vue SHALL 移除 createProject、openProject、saveProject、generateAnimation 等小说动漫相关函数
7. WHEN 清理完成, THE Frontend_App 的 App.vue SHALL 将侧边栏标签从 "NOVEL ANIME" 更改为通用标签（如 "ERP" 或 "MENU"）

### Requirement 7: 移除前端 Novel-Anime 测试

**User Story:** As a 开发者, I want to 移除所有小说动漫相关的测试文件, so that 测试套件只包含保留基础设施的测试。

#### Acceptance Criteria

1. WHEN 清理完成, THE Frontend_App SHALL 不包含以下测试文件: `services/__tests__/AIService.test.ts`、`services/__tests__/BackupManager.test.ts`、`services/__tests__/domainMigration.test.ts`、`services/errorHandler.spec.ts`、`services/workflowService.spec.ts`、`stores/__tests__/workflowStore.checkpoint.test.ts`、`stores/workflowStore.spec.ts`
2. WHEN 清理完成, THE Frontend_App SHALL 保留以下核心测试文件: `services/__tests__/apiV1Client.test.ts`、`services/__tests__/authServiceV1.test.ts`、`services/__tests__/tokenManager.test.ts`、`services/__tests__/apiDiscoveryService.test.ts`、`services/__tests__/entityService.test.ts`、`composables/useAppInit.spec.ts`
3. WHEN 清理完成, THE Preserved_Tests SHALL 全部通过 `npm test`

### Requirement 8: 移除后端 Novel-Anime 控制器和服务

**User Story:** As a 开发者, I want to 移除后端所有小说动漫相关的控制器和服务类, so that REST API 只保留核心 ERP 和基础设施端点。

#### Acceptance Criteria

1. WHEN 清理完成, THE Backend_API SHALL 不包含以下控制器: NovelController、CharacterController、SceneController、EpisodeController、ProjectController、CreditsController、WorkflowController、AssetController
2. WHEN 清理完成, THE Backend_API SHALL 不包含以下服务类: NovelService、CharacterService、SceneService、EpisodeService、ProjectService、CreditsService
3. WHEN 清理完成, THE RestApplication SHALL 不再实例化和注册已移除的控制器和服务
4. WHEN 清理完成, THE Backend_API SHALL 保留以下核心控制器: AuthController、EntityController、ServiceController、ScreenController、DocsController、MetaController、ApiCatalogController、MonitoringController
5. WHEN 清理完成, THE Backend_API SHALL 保留以下核心服务: AuthService、EntityCrudService、EntityCatalogService、ServiceInvocationService、ServiceCatalogService、ScreenCatalogService、ScreenService、ApiMetricsService 及所有辅助类

### Requirement 9: 后端测试和构建验证

**User Story:** As a 开发者, I want to 确保后端清理后所有保留的测试仍然通过, so that 核心功能不受影响。

#### Acceptance Criteria

1. WHEN 清理完成, THE Backend_API SHALL 通过 `mvn test` 且所有保留测试全部通过
2. WHEN 清理完成, THE Backend_API SHALL 保留所有安全相关测试（PBT、JUnit、E2E）不受影响
3. IF 后端测试中存在对已移除端点的引用, THEN THE Backend_API SHALL 更新或移除这些测试引用以确保编译通过

### Requirement 10: 文档和 Steering 更新

**User Story:** As a 开发者, I want to 更新项目文档和 Steering 文件, so that 文档反映清理后的项目状态。

#### Acceptance Criteria

1. WHEN 清理完成, THE ENVIRONMENT.md SHALL 更新测试计数以反映清理后的实际数量
2. WHEN 清理完成, THE CURRENT_CONTEXT.md SHALL 更新为包含 21-00 Spec 的完成状态和新的架构描述
3. WHEN 清理完成, THE Frontend_App 的 README（如有）SHALL 更新为反映清理后的应用描述
