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

# Design Document: Novel-Anime Deep Cleanup

## Overview

This spec performs a systematic find-and-replace cleanup across the frontend codebase to remove all remaining `novel-anime` / `小说动漫` references. The work is purely mechanical renaming and deletion — no new features, no architectural changes. Each file is modified in isolation with consistent replacement patterns.

**Key Replacement Patterns:**
| Legacy Pattern | Replacement |
|---|---|
| `NovelAnimeUser` | `ERPUser` |
| `novel_anime_*` (localStorage keys) | `moqui_erp_*` |
| `novel-anime-settings` (localStorage key) | `moqui-erp-settings` |
| `/rest/s1/novel-anime/` (API paths) | `/api/v1/` |
| `Novel Anime Generator` / `小说动漫生成器` | `Moqui ERP` / `331-POC ERP` |
| `NovelAnime` (directory paths) | `MoquiERP` |
| `Novel` / `NovelChapter` interfaces | Remove entirely |
| `novelId` fields | Remove entirely |

## Architecture

No architectural changes. This is a rename/delete operation across existing files. The affected layers are:

1. **Stores layer** — `auth.ts`, `user.ts`, `settings.js`, `notification.ts`
2. **Services layer** — `api.ts`, `authApi.ts`, `authServiceV1.ts`, `apiV1Client.ts`, `tokenManager.ts`
3. **Types layer** — `types/api.ts`
4. **Styles layer** — `design-system.scss`
5. **Entry point** — `renderer/main.js`
6. **Legacy components** — `AssetBrowser.vue`, `AssetInfoDialog.vue`, `AssetPreviewDialog.vue`, `AssetUploadDialog.vue`, `SettingsPanel.vue`

## Components and Interfaces

### Renamed Interface

```typescript
// BEFORE
export interface NovelAnimeUser {
  userId: string
  email: string
  username: string
  credits: number
  avatarUrl: string | null
  authProvider: 'local' | 'github' | 'google' | 'wechat'
  status: string
  createdDate: string
  lastLoginDate: string
}

// AFTER
export interface ERPUser {
  userId: string
  email: string
  username: string
  credits: number
  avatarUrl: string | null
  authProvider: 'local' | 'github' | 'google' | 'wechat'
  status: string
  createdDate: string
  lastLoginDate: string
}
```

### Removed Interfaces (from types/api.ts)

```typescript
// REMOVED entirely
export interface Novel { ... }
export interface NovelChapter { ... }

// REMOVED novelId field from:
export interface Asset { novelId?: string; ... }        // remove novelId
export interface CreateAssetRequest { novelId?: string; ... }  // remove novelId
```

### localStorage Key Mapping

| Old Key | New Key |
|---|---|
| `novel_anime_access_token` | `moqui_erp_access_token` |
| `novel_anime_refresh_token` | `moqui_erp_refresh_token` |
| `novel_anime_user_id` | `moqui_erp_user_id` |
| `novel_anime_user_data` | `moqui_erp_user_data` |
| `novel-anime-settings` | `moqui-erp-settings` |

### API Endpoint Mapping

| Old Endpoint | New Endpoint |
|---|---|
| `/rest/s1/novel-anime/auth/register` | `/api/v1/auth/register` |
| `/rest/s1/novel-anime/auth/oauth/github/url` | `/api/v1/auth/oauth/github/url` |
| `/rest/s1/novel-anime/auth/oauth/github` | `/api/v1/auth/oauth/github` |
| `/rest/s1/novel-anime/auth/oauth/google/url` | `/api/v1/auth/oauth/google/url` |
| `/rest/s1/novel-anime/auth/oauth/google` | `/api/v1/auth/oauth/google` |
| `/rest/s1/novel-anime/auth/wechat/qrcode` | `/api/v1/auth/wechat/qrcode` |
| `/rest/s1/novel-anime/auth/wechat/status` | `/api/v1/auth/wechat/status` |
| `/rest/s1/novel-anime/auth/profile` | `/api/v1/auth/profile` |
| `/rest/s1/novel-anime/auth/change-password` | `/api/v1/auth/change-password` |
| `/rest/s1/novel-anime/auth/avatar` | `/api/v1/auth/avatar` |
| `/rest/s1/novel-anime/characters` | `/api/v1/characters` |
| `/rest/s1/novel-anime/asset/{id}/references` | `/api/v1/asset/{id}/references` |
| `/rest/s1/novel-anime/asset-file/{path}` | `/api/v1/asset-file/{path}` |
| `/rest/s1/novel-anime/assets/upload` | `/api/v1/assets/upload` |

## Data Models

No data model changes. The `ERPUser` interface retains the same fields as `NovelAnimeUser` — only the name changes. The `Novel` and `NovelChapter` interfaces are removed entirely as they are unused legacy types.

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system — essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

This spec is a mechanical rename/delete cleanup. All acceptance criteria are static code verification checks (example-based), not universal properties over random inputs. The prework analysis confirms no property-based tests are warranted.

**Verification is achieved through:**
1. A comprehensive codebase-wide grep for legacy patterns (subsumes all individual file checks)
2. Running the existing frontend test suite to confirm no regressions

No property-based tests are defined for this spec.

## Error Handling

- If a localStorage key rename causes data loss for existing users, the old keys should be read as a one-time migration fallback. However, since this is a development/POC project with no production users, migration is not required — clean replacement is sufficient.
- If removing `Novel`/`NovelChapter` types causes TypeScript compilation errors in other files, those files must also be updated to remove the references.
- If removing `getNovel`/`getNovels` methods causes compilation errors, callers must be identified and updated.

## Testing Strategy

**Unit Tests:** No new unit tests. The existing 27 frontend tests validate that the rename operations don't break functionality.

**Verification Steps:**
1. Run `npm test` in `frontend/` — all 27 tests must pass
2. Run a codebase-wide grep for `novel.anime|novel_anime|NovelAnime|Novel Anime|小说动漫|小说动漫生成器` in `frontend/src/` — must return zero results
3. Run TypeScript compilation check via diagnostics — no type errors

**Property-Based Tests:** None. This is a cleanup spec with no new logic to validate via PBT.
