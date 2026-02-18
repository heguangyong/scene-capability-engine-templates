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

# Implementation Plan: Novel-Anime Deep Cleanup

## Overview

Systematic find-and-replace cleanup of all remaining novel-anime references across `frontend/src/`. Each task targets a specific file or group of files. Files 7-10 from the original list (main.js, FileAccessControl.js, LoginView.vue, Settings.vue) are already fixed and excluded.

## Tasks

- [x] 1. Clean up auth store and token manager
  - [x] 1.1 Rename NovelAnimeUser to ERPUser in `frontend/src/renderer/stores/auth.ts`
    - Rename interface `NovelAnimeUser` → `ERPUser`
    - Replace all `as NovelAnimeUser` casts → `as ERPUser`
    - Replace `user: NovelAnimeUser | null` → `user: ERPUser | null` in AuthState
    - Replace all `novel_anime_*` localStorage keys → `moqui_erp_*`
    - Update file header comment to "ERP Authentication Store"
    - _Requirements: 1.1, 1.2, 1.3, 2.1_

  - [x] 1.2 Replace localStorage keys in `frontend/src/renderer/services/tokenManager.ts`
    - Replace `novel_anime_access_token` → `moqui_erp_access_token`
    - Replace `novel_anime_refresh_token` → `moqui_erp_refresh_token`
    - Replace `novel_anime_user_id` → `moqui_erp_user_id`
    - Replace `novel_anime_user_data` → `moqui_erp_user_data`
    - _Requirements: 2.2_

  - [x] 1.3 Replace localStorage keys and comments in `frontend/src/renderer/stores/user.ts`
    - Replace `novel_anime_user_data` → `moqui_erp_user_data`
    - Replace `novel_anime_access_token` → `moqui_erp_access_token`
    - Replace `novel_anime_refresh_token` → `moqui_erp_refresh_token`
    - Replace `novel_anime_user_id` → `moqui_erp_user_id`
    - Update file header comment: remove "Novel Anime" references, use "ERP User Store"
    - Remove `NovelAnimeUser` references in comments
    - _Requirements: 2.4, 6.4_

- [x] 2. Clean up API services
  - [x] 2.1 Clean up `frontend/src/renderer/services/api.ts`
    - Replace default base URL `http://localhost:8080/rest/s1/novel-anime` → `http://localhost:8080/api/v1`
    - Replace all `novel_anime_*` localStorage keys → `moqui_erp_*`
    - Update comment "API Service for Novel Anime Generator" → "API Service for Moqui ERP"
    - Remove `getNovel` and `getNovels` methods entirely
    - Remove `novelId` from `getAssets` params and `createAsset` data types
    - _Requirements: 2.3, 3.1, 3.4, 7.1, 7.2, 7.3_

  - [x] 2.2 Clean up `frontend/src/renderer/services/authApi.ts`
    - Replace all `/rest/s1/novel-anime/auth/*` endpoint paths → `/api/v1/auth/*`
    - Replace `novel_anime_user_data` localStorage key → `moqui_erp_user_data`
    - _Requirements: 2.5, 4.1, 4.2, 4.3, 4.4_

  - [x] 2.3 Clean up `frontend/src/renderer/services/authServiceV1.ts`
    - Replace legacy base URL `http://localhost:8080/rest/s1/novel-anime` → `http://localhost:8080/api/v1`
    - _Requirements: 3.3_

  - [x] 2.4 Clean up `frontend/src/renderer/services/apiV1Client.ts`
    - Replace legacy fallback URL `http://localhost:8080/rest/s1/novel-anime` → `http://localhost:8080/api/v1`
    - _Requirements: 3.2_

- [x] 3. Clean up types and remove Novel interfaces
  - [x] 3.1 Clean up `frontend/src/renderer/types/api.ts`
    - Remove `Novel` interface entirely
    - Remove `NovelChapter` interface entirely
    - Remove `novelId` field from `Asset` interface
    - Remove `novelId` field from `CreateAssetRequest` interface
    - Remove Chinese comments referencing 小说 (novel)
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [x] 4. Clean up supporting files
  - [x] 4.1 Clean up `frontend/src/renderer/stores/notification.ts`
    - Replace "欢迎使用小说动漫生成器" → "欢迎使用 331-POC ERP"
    - _Requirements: 6.1_

  - [x] 4.2 Clean up `frontend/src/renderer/stores/settings.js`
    - Replace `novel-anime-settings` localStorage key → `moqui-erp-settings`
    - Replace export filename `novel-anime-settings.json` → `moqui-erp-settings.json`
    - Replace `~/Documents/NovelAnime/Projects` → `~/Documents/MoquiERP/Projects`
    - Replace `~/Documents/NovelAnime/Cache` → `~/Documents/MoquiERP/Cache`
    - _Requirements: 2.6, 6.5, 6.6_

  - [x] 4.3 Clean up `frontend/src/renderer/styles/design-system.scss`
    - Replace "小说动漫生成器 UI 设计系统" → "331-POC ERP UI 设计系统"
    - _Requirements: 6.2_

  - [x] 4.4 Clean up `frontend/src/renderer/main.js`
    - Replace "Starting Novel Anime Desktop App" → "Starting Moqui ERP Desktop App"
    - _Requirements: 6.3_

- [x] 5. Clean up legacy Vue components
  - [x] 5.1 Clean up `frontend/src/renderer/components/panels/AssetBrowser.vue`
    - Replace `/rest/s1/novel-anime/characters` URL → `/api/v1/characters`
    - _Requirements: 7.4_

  - [x] 5.2 Clean up `frontend/src/renderer/components/dialogs/AssetInfoDialog.vue`
    - Replace `/rest/s1/novel-anime/asset/` URL → `/api/v1/asset/`
    - _Requirements: 7.5_

  - [x] 5.3 Clean up `frontend/src/renderer/components/dialogs/AssetPreviewDialog.vue`
    - Replace `/rest/s1/novel-anime/asset-file/` URL → `/api/v1/asset-file/`
    - _Requirements: 7.6_

  - [x] 5.4 Clean up `frontend/src/renderer/components/dialogs/AssetUploadDialog.vue`
    - Replace `/rest/s1/novel-anime/assets/upload` URL → `/api/v1/assets/upload`
    - _Requirements: 7.7_

  - [x] 5.5 Clean up `frontend/src/renderer/components/panels/SettingsPanel.vue`
    - Replace `novel-anime-settings.json` → `moqui-erp-settings.json`
    - _Requirements: 7.8_

- [x] 6. Verification checkpoint
  - Run codebase-wide grep for `novel.anime|novel_anime|NovelAnime|Novel Anime|小说动漫` in `frontend/src/` — must return zero results
  - Run `npm test` in `frontend/` — all 27 tests must pass
  - _Requirements: 8.1, 8.2_
