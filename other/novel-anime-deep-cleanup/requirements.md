---
name: novel-anime-deep-cleanup
category: other
description: Template for Novel Anime Deep Cleanup
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

Thorough cleanup of ALL remaining novel-anime/小说动漫 references across the entire frontend codebase (`frontend/src/`). Spec 21-00 previously cleaned up novel-anime business code (views, components, stores, services, controllers), but missed many references in core infrastructure files including auth stores, API services, token management, type definitions, settings, notifications, and legacy Vue components. This spec ensures the codebase consistently reflects the project's identity as "331-POC ERP" / "Moqui ERP" with no legacy novel-anime naming artifacts remaining.

## Glossary

- **Frontend_Codebase**: All source files under `frontend/src/`, including renderer, main process, styles, and types
- **Legacy_Reference**: Any occurrence of `novel-anime`, `novel_anime`, `NovelAnime`, `Novel Anime`, `小说动漫`, `小说`, `动漫`, `Novel`, `NovelChapter`, or `novelId` that originates from the old project identity
- **ERP_Identity**: The current project identity using names like `moqui_erp`, `MoquiERP`, `ERPUser`, `331-POC ERP`, or `Moqui ERP`
- **localStorage_Key**: A string key used with `localStorage.getItem()` / `localStorage.setItem()` for persisting data in the browser
- **Legacy_Endpoint**: Any API URL path containing `/rest/s1/novel-anime/`
- **Auth_Store**: The Pinia store in `frontend/src/renderer/stores/auth.ts` managing authentication state
- **Token_Manager**: The service in `frontend/src/renderer/services/tokenManager.ts` managing JWT token storage and refresh
- **API_Service**: The service in `frontend/src/renderer/services/api.ts` providing HTTP communication with the backend
- **Auth_API_Service**: The service in `frontend/src/renderer/services/authApi.ts` providing legacy authentication endpoints
- **Type_Definitions**: TypeScript interfaces and types in `frontend/src/renderer/types/api.ts`

## Requirements

### Requirement 1: Rename NovelAnimeUser to ERPUser in Auth Store

**User Story:** As a developer, I want the auth store to use ERP-appropriate naming, so that the codebase consistently reflects the current project identity.

#### Acceptance Criteria

1. WHEN the Auth_Store is loaded, THE Auth_Store SHALL define the user interface as `ERPUser` instead of `NovelAnimeUser`
2. WHEN the Auth_Store references the user type in state, actions, or type casts, THE Auth_Store SHALL use `ERPUser` consistently throughout the file
3. WHEN the Auth_Store file header comment is read, THE Auth_Store SHALL describe itself as "ERP Authentication Store" with no novel-anime references in comments

### Requirement 2: Replace All novel_anime localStorage Keys

**User Story:** As a developer, I want all localStorage keys to use the `moqui_erp_` prefix, so that stored data keys are consistent with the project identity.

#### Acceptance Criteria

1. WHEN the Auth_Store reads or writes localStorage, THE Auth_Store SHALL use keys prefixed with `moqui_erp_` instead of `novel_anime_`
2. WHEN the Token_Manager reads or writes localStorage, THE Token_Manager SHALL use keys prefixed with `moqui_erp_` instead of `novel_anime_`
3. WHEN the API_Service reads or writes localStorage, THE API_Service SHALL use keys prefixed with `moqui_erp_` instead of `novel_anime_`
4. WHEN the User Store reads or writes localStorage, THE User Store SHALL use keys prefixed with `moqui_erp_` instead of `novel_anime_`
5. WHEN the Auth_API_Service reads localStorage for user data, THE Auth_API_Service SHALL use the `moqui_erp_user_data` key instead of `novel_anime_user_data`
6. WHEN the Settings Store reads or writes localStorage, THE Settings Store SHALL use the key `moqui-erp-settings` instead of `novel-anime-settings`

### Requirement 3: Remove Legacy novel-anime API Base URLs

**User Story:** As a developer, I want all legacy `/rest/s1/novel-anime` base URLs removed, so that the application exclusively uses the current `/api/v1/` API endpoints.

#### Acceptance Criteria

1. WHEN the API_Service constructs its base URL, THE API_Service SHALL default to `http://localhost:8080/api/v1` instead of any URL containing `novel-anime`
2. WHEN the ApiV1Client resolves its base URL in legacy mode, THE ApiV1Client SHALL default to `http://localhost:8080/api/v1` instead of any URL containing `novel-anime`
3. WHEN the AuthServiceV1 register method constructs a legacy base URL, THE AuthServiceV1 SHALL use `/api/v1` instead of `/rest/s1/novel-anime`
4. WHEN the API_Service comment header is read, THE API_Service SHALL describe itself as "API Service for Moqui ERP" with no novel-anime references

### Requirement 4: Remove Legacy novel-anime Auth Endpoints

**User Story:** As a developer, I want all legacy `/rest/s1/novel-anime/auth/*` endpoint paths removed from authApi.ts, so that the service uses current API paths.

#### Acceptance Criteria

1. WHEN the Auth_API_Service calls the register endpoint, THE Auth_API_Service SHALL use `/api/v1/auth/register` instead of `/rest/s1/novel-anime/auth/register`
2. WHEN the Auth_API_Service calls OAuth endpoints (GitHub, Google, WeChat), THE Auth_API_Service SHALL use `/api/v1/auth/oauth/*` paths instead of `/rest/s1/novel-anime/auth/oauth/*` paths
3. WHEN the Auth_API_Service calls profile management endpoints (updateProfile, changePassword, uploadAvatar), THE Auth_API_Service SHALL use `/api/v1/auth/*` paths instead of `/rest/s1/novel-anime/auth/*` paths
4. WHEN the Auth_API_Service calls the WeChat QR code and status endpoints, THE Auth_API_Service SHALL use `/api/v1/auth/wechat/*` paths instead of `/rest/s1/novel-anime/auth/wechat/*` paths

### Requirement 5: Remove Novel/NovelChapter Types and novelId Fields

**User Story:** As a developer, I want legacy Novel and NovelChapter type definitions and novelId fields removed from the type definitions, so that the type system reflects only current ERP domain models.

#### Acceptance Criteria

1. WHEN the Type_Definitions file is compiled, THE Type_Definitions SHALL not contain the `Novel` interface
2. WHEN the Type_Definitions file is compiled, THE Type_Definitions SHALL not contain the `NovelChapter` interface
3. WHEN the Type_Definitions file is compiled, THE Asset and CreateAssetRequest interfaces SHALL not contain `novelId` fields
4. WHEN the Type_Definitions file comments are read, THE Type_Definitions SHALL not contain Chinese comments referencing 小说 (novel) concepts

### Requirement 6: Clean Up Remaining Novel-Anime References in Supporting Files

**User Story:** As a developer, I want all remaining novel-anime references in supporting files cleaned up, so that no legacy naming artifacts remain anywhere in the frontend codebase.

#### Acceptance Criteria

1. WHEN the notification store welcome message is displayed, THE Notification Store SHALL show "欢迎使用 331-POC ERP" instead of "欢迎使用小说动漫生成器"
2. WHEN the design-system.scss file header comment is read, THE design-system.scss SHALL describe itself as "331-POC ERP UI 设计系统" instead of "小说动漫生成器 UI 设计系统"
3. WHEN the renderer main.js startup log is printed, THE main.js SHALL log "Starting Moqui ERP Desktop App" instead of "Starting Novel Anime Desktop App"
4. WHEN the User Store file header comment is read, THE User Store SHALL describe itself as "ERP User Store" with no novel-anime references in comments
5. WHEN the settings store exports settings, THE Settings Store SHALL use the filename `moqui-erp-settings.json` instead of `novel-anime-settings.json`
6. WHEN the settings store default storage paths are read, THE Settings Store SHALL use `~/Documents/MoquiERP/Projects` and `~/Documents/MoquiERP/Cache` instead of paths containing `NovelAnime`

### Requirement 7: Remove Legacy Novel API Methods and Asset Novel References

**User Story:** As a developer, I want legacy Novel API methods and novelId references in the API service removed, so that the API service only contains current ERP functionality.

#### Acceptance Criteria

1. WHEN the API_Service is inspected, THE API_Service SHALL not contain `getNovel` or `getNovels` methods
2. WHEN the API_Service `getAssets` method accepts parameters, THE API_Service SHALL not accept a `novelId` parameter
3. WHEN the API_Service `createAsset` method accepts data, THE API_Service SHALL not accept a `novelId` field
4. WHEN the legacy Vue component AssetBrowser fetches data, THE AssetBrowser SHALL not reference `/rest/s1/novel-anime/characters` URLs
5. WHEN the legacy Vue component AssetInfoDialog fetches references, THE AssetInfoDialog SHALL not reference `/rest/s1/novel-anime/asset/` URLs
6. WHEN the legacy Vue component AssetPreviewDialog constructs asset URLs, THE AssetPreviewDialog SHALL not reference `/rest/s1/novel-anime/asset-file/` URLs
7. WHEN the legacy Vue component AssetUploadDialog uploads files, THE AssetUploadDialog SHALL not reference `/rest/s1/novel-anime/assets/upload` URLs
8. WHEN the SettingsPanel exports settings, THE SettingsPanel SHALL use the filename `moqui-erp-settings.json` instead of `novel-anime-settings.json`

### Requirement 8: Verification of Complete Cleanup

**User Story:** As a developer, I want to verify that no novel-anime references remain anywhere in the frontend codebase, so that the cleanup is confirmed complete.

#### Acceptance Criteria

1. WHEN a text search for `novel.anime`, `novel_anime`, `NovelAnime`, `Novel Anime`, `小说动漫`, or `小说动漫生成器` is performed across the Frontend_Codebase, THE search SHALL return zero results
2. WHEN the existing frontend test suite is executed, THE test suite SHALL pass with no regressions
