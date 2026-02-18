---
name: dashboard-enhancement
category: other
description: Template for Dashboard Enhancement
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

{{SPEC_NAME_TITLE}} 前端模块，为 331-poc ERP 现代化项目的 DashboardView 添加实时系统目录统计和快速导航能力。用户登录后在 Dashboard 首页即可看到 Entity、Service、Screen 的总数统计，点击统计卡片可直接跳转到对应的 Explorer 页面。同时展示组件级别的数量明细（如 framework 有多少 Entity、mantle-usl 有多少 Service 等），并提供快速导航卡片直达各 Explorer 页面。该模块复用已有的 `apiDiscoveryService.getCatalog()` API 客户端和后端 `/api/v1/catalog` REST API，无需后端改动。

## Glossary

- **Dashboard_Store**: Pinia Store，管理 Dashboard 页面的目录统计数据：catalog 数据、加载状态、错误状态
- **Dashboard_View**: 已有的 DashboardView.vue 组件，登录后的首页，本次增强的目标组件
- **Catalog_Stats_Section**: 目录统计区域，展示 Entity/Service/Screen 三个统计卡片
- **Stat_Card**: 单个统计卡片组件，展示某类资源的总数，可点击跳转到对应 Explorer
- **Component_Breakdown_Section**: 组件明细区域，按组件维度展示各类资源的数量分布
- **Quick_Nav_Section**: 快速导航区域，提供跳转到各 Explorer 页面的导航卡片
- **Api_Discovery_Service**: 已有的前端 API 客户端（apiDiscoveryService.ts），提供 `getCatalog()` 方法
- **Api_Catalog_Payload**: 后端 `/api/v1/catalog` 返回的数据结构，包含 serviceCatalog、entityCatalog、screenCatalog

## Requirements

### Requirement 1: Dashboard Catalog State Management

**User Story:** As a developer, I want a dedicated Pinia store for dashboard catalog data, so that the DashboardView can reactively display catalog statistics with proper loading and error states.

#### Acceptance Criteria

1. THE Dashboard_Store SHALL maintain the catalog data (ApiCatalogPayload), loading state, and error state
2. THE Dashboard_Store SHALL provide a `fetchCatalog` action that calls Api_Discovery_Service.getCatalog and stores the result
3. WHEN fetchCatalog starts, THE Dashboard_Store SHALL set loading to true and clear the previous error
4. WHEN fetchCatalog completes successfully, THE Dashboard_Store SHALL set loading to false and store the catalog data
5. IF fetchCatalog fails, THEN THE Dashboard_Store SHALL set loading to false and store the error message
6. THE Dashboard_Store SHALL provide computed getters for totalEntities, totalServices, totalScreens derived from the stored catalog data
7. THE Dashboard_Store SHALL provide a computed getter for the component breakdown list combining entity, service, and screen counts per component

### Requirement 2: Catalog Statistics Display

**User Story:** As a user, I want to see Entity, Service, and Screen counts on the Dashboard, so that I can quickly understand the system's catalog scale.

#### Acceptance Criteria

1. WHEN the Dashboard_View mounts and the user is authenticated, THE Dashboard_View SHALL trigger Dashboard_Store.fetchCatalog to load catalog data
2. THE Catalog_Stats_Section SHALL display three Stat_Card components for Entity 总数, Service 总数, and Screen 总数
3. WHEN catalog data is loaded, THE Stat_Card SHALL display the corresponding count number prominently
4. WHILE catalog data is loading, THE Catalog_Stats_Section SHALL display a loading skeleton placeholder for each Stat_Card
5. IF catalog data fails to load, THEN THE Catalog_Stats_Section SHALL display an error message with a retry button

### Requirement 3: Stat Card Navigation

**User Story:** As a user, I want to click a stat card to navigate to the corresponding Explorer page, so that I can quickly drill into the details.

#### Acceptance Criteria

1. WHEN a user clicks the Entity Stat_Card, THE Dashboard_View SHALL navigate to the `/entities` route
2. WHEN a user clicks the Service Stat_Card, THE Dashboard_View SHALL navigate to the `/services` route
3. WHEN a user clicks the Screen Stat_Card, THE Dashboard_View SHALL navigate to the `/screens` route
4. THE Stat_Card SHALL display a hover effect indicating the card is clickable

### Requirement 4: Component Breakdown Display

**User Story:** As a user, I want to see a component-level breakdown of catalog counts, so that I can understand how resources are distributed across components.

#### Acceptance Criteria

1. THE Component_Breakdown_Section SHALL display a table or list showing each component name with its entity count, service count, and screen count
2. THE Component_Breakdown_Section SHALL be collapsible, defaulting to collapsed state
3. WHEN a user expands the Component_Breakdown_Section, THE Dashboard_View SHALL display the full component breakdown data
4. WHILE catalog data is loading, THE Component_Breakdown_Section SHALL be hidden or display a loading indicator

### Requirement 5: Quick Navigation Cards

**User Story:** As a user, I want quick navigation cards on the Dashboard, so that I can jump directly to the Explorer pages without using the sidebar.

#### Acceptance Criteria

1. THE Quick_Nav_Section SHALL display navigation cards for Screen Explorer, Entity Explorer, and Service Explorer
2. THE Quick_Nav_Section SHALL display each card with an icon, title, and brief description
3. WHEN a user clicks the Screen Explorer card, THE Dashboard_View SHALL navigate to the `/screens` route
4. WHEN a user clicks the Entity Explorer card, THE Dashboard_View SHALL navigate to the `/entities` route
5. WHEN a user clicks the Service Explorer card, THE Dashboard_View SHALL navigate to the `/services` route

### Requirement 6: Dark Theme Support

**User Story:** As a user, I want the enhanced Dashboard to support dark theme, so that the new sections are visually consistent with the rest of the application.

#### Acceptance Criteria

1. THE Dashboard_View SHALL apply dark theme styles to all new sections (Catalog_Stats_Section, Component_Breakdown_Section, Quick_Nav_Section) using `[data-theme="dark"]` CSS selectors
2. THE Stat_Card SHALL use CSS variables consistent with the existing Dashboard_View style patterns (--color-bg-surface, --color-text-primary, --color-border, etc.)
