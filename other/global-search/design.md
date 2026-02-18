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

# 全局搜索 — 设计文档

## 架构概述

全局搜索包含后端搜索聚合 API 和前端搜索对话框组件。后端负责跨 entity 查询，前端负责 UI 交互和键盘导航。

---

## 后端设计

### SearchService

```java
public class SearchService {
    private final ExecutionContextFactory ecf;
    
    // 搜索模块配置
    private static final Map<String, SearchModuleConfig> MODULE_CONFIGS = Map.ofEntries(
        Map.entry("product", new SearchModuleConfig("mantle.product.Product", "productId", "productName", null)),
        Map.entry("order", new SearchModuleConfig("mantle.order.OrderHeader", "orderId", "orderName", "placedDate")),
        Map.entry("party", new SearchModuleConfig("mantle.party.Party", "partyId", "partyName", null)),
        Map.entry("invoice", new SearchModuleConfig("mantle.account.invoice.Invoice", "invoiceId", "description", null)),
        Map.entry("facility", new SearchModuleConfig("mantle.facility.Facility", "facilityId", "facilityName", null)),
        Map.entry("shipment", new SearchModuleConfig("mantle.shipment.Shipment", "shipmentId", "shipmentTypeEnumId", null)),
        Map.entry("bom", new SearchModuleConfig("moqui.api.BomHeader", "bomId", "bomName", null)),
        Map.entry("procurement", new SearchModuleConfig("mantle.order.OrderHeader", "orderId", "orderName", null)),
        Map.entry("inventory", new SearchModuleConfig("mantle.product.asset.Asset", "assetId", "assetName", null))
    );
    
    public Map<String, Object> search(String keyword, String modules, int maxPerModule);
}
```

每个模块搜索逻辑：
- 用 `like` 条件匹配名称/编号字段
- 每个模块最多返回 `maxPerModule` 条（默认 5）
- 结果按模块分组

### SearchController

```
GET /api/v1/search?q={keyword}&modules={module1,module2}&limit={5}
```

### RestApplication 注册

```java
SearchService searchService = new SearchService(ecf);
new SearchController(searchService).register(router);
```

---

## 前端设计

### 搜索对话框组件 (GlobalSearchDialog.vue)

位置：`frontend/src/renderer/components/dialogs/GlobalSearchDialog.vue`

功能：
- Ctrl+K 快捷键触发（在 App.vue 中注册全局监听）
- 居中模态对话框，半透明遮罩
- 搜索输入框 + 结果列表
- 300ms 防抖
- 键盘导航（↑↓ 选择，Enter 跳转，ESC 关闭）
- 结果按模块分组，每组带模块标签和图标

### 搜索 Store (stores/search.ts)

```typescript
// State
const keyword = ref('');
const results = ref<SearchResultGroup[]>([]);
const loading = ref(false);

// Actions
async function search(q: string): Promise<void>;
function clearResults(): void;
```

### 类型定义 (types/search.ts)

```typescript
export interface SearchResult {
  id: string;
  name: string;
  description?: string;
  module: string;
  route: string;
}

export interface SearchResultGroup {
  module: string;
  label: string;
  icon: Component;
  items: SearchResult[];
}
```

### 模块路由映射

```typescript
const MODULE_ROUTE_MAP: Record<string, (id: string) => string> = {
  product: (id) => `/product/${id}`,
  order: (id) => `/order/${id}`,
  party: (id) => `/party/${id}`,
  invoice: (id) => `/invoice/${id}`,
  facility: (id) => `/facility/${id}`,
  shipment: (id) => `/shipment/${id}`,
  bom: (id) => `/bom/${id}`,
  routing: (id) => `/routing/${id}`,
  'production-run': (id) => `/production-run/${id}`,
  procurement: (id) => `/procurement/${id}`,
  inventory: (id) => `/inventory/${id}`,
};

const MODULE_LABELS: Record<string, string> = {
  product: '产品',
  order: '订单',
  party: 'Party',
  invoice: '发票',
  facility: '设施',
  shipment: '发货',
  bom: 'BOM',
  routing: '工艺路线',
  'production-run': '生产工单',
  procurement: '采购',
  inventory: '库存',
};
```

---

## 改动文件清单

| 文件 | 操作 |
|------|------|
| `backend/.../service/SearchService.java` | 新增 |
| `backend/.../controller/SearchController.java` | 新增 |
| `backend/.../RestApplication.java` | 修改 |
| `frontend/src/renderer/types/search.ts` | 新增 |
| `frontend/src/renderer/stores/search.ts` | 新增 |
| `frontend/src/renderer/components/dialogs/GlobalSearchDialog.vue` | 新增 |
| `frontend/src/renderer/App.vue` | 修改（注册快捷键 + 引入对话框） |
