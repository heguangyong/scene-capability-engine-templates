---
name: accounting-invoice
category: other
description: Template for Accounting Invoice
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 财务管理模块 — 设计文档

## 1. 架构概览
遵循项目现有模式：Service → Controller → Router 注册（后端），Types → ApiService → Store → Views（前端）。

## 2. 后端设计

### 2.1 InvoiceService.java
- 包路径: `org.moqui.rest.service.InvoiceService`
- 构造函数注入 `ExecutionContextFactory`
- 方法:
  - `listInvoices(pageIndex, pageSize, invoiceTypeEnumId, statusId, searchKeyword)` → 分页查询 `mantle.account.invoice.Invoice`
  - `getInvoiceDetail(invoiceId)` → 查询发票头 + InvoiceItem 列表
  - `createInvoice(data)` → 创建 Invoice，初始状态 InvoiceInProcess，自动设置 invoiceDate
  - `updateInvoice(invoiceId, data)` → 更新 Invoice 字段
  - `updateStatus(invoiceId, statusId)` → 更新发票状态
  - `addItem(invoiceId, data)` → 创建 InvoiceItem，自动生成 invoiceItemSeqId
  - `updateItem(invoiceId, invoiceItemSeqId, data)` → 更新 InvoiceItem
  - `deleteItem(invoiceId, invoiceItemSeqId)` → 删除 InvoiceItem
  - `listPayments(pageIndex, pageSize, paymentTypeEnumId, statusId)` → 分页查询 Payment
- 错误处理: 使用 `errorResult(errorCode, errorMessage)` 模式

### 2.2 InvoiceController.java
- 包路径: `org.moqui.rest.controller.InvoiceController`
- 路由注册:
  ```
  GET    /api/v1/invoice                                                → listInvoices
  GET    /api/v1/invoice/{invoiceId}                                    → getInvoiceDetail
  POST   /api/v1/invoice                                                → createInvoice
  PUT    /api/v1/invoice/{invoiceId}                                    → updateInvoice
  POST   /api/v1/invoice/{invoiceId}/update-status                      → updateStatus
  POST   /api/v1/invoice/{invoiceId}/items                              → addItem
  PUT    /api/v1/invoice/{invoiceId}/items/{invoiceItemSeqId}           → updateItem
  DELETE /api/v1/invoice/{invoiceId}/items/{invoiceItemSeqId}           → deleteItem
  GET    /api/v1/payment                                                → listPayments
  ```

### 2.3 RestApplication.java 注册
```java
InvoiceService invoiceService = new InvoiceService(ecf);
new InvoiceController(invoiceService).register(router);
```

## 3. 前端设计

### 3.1 类型定义 — `types/invoice.ts`
- InvoiceListItem, InvoiceDetail, InvoiceItemEntry, PaymentListItem
- CreateInvoiceRequest, UpdateInvoiceRequest, InvoiceItemRequest
- InvoiceListParams, InvoicePaginationMeta

### 3.2 API 服务 — `services/invoiceApiService.ts`
- 使用 `apiV1Client`，路径前缀: `/invoice` 和 `/payment`

### 3.3 Pinia Store — `stores/invoice.ts`
- 状态: invoices, pagination, loading, error, currentInvoice, detailLoading, payments, mutating
- Actions: fetchInvoices, searchInvoices, fetchInvoiceDetail, createInvoice, updateInvoice, updateStatus, addItem, updateItem, deleteItem, fetchPayments

### 3.4 视图组件
- `InvoiceListView.vue` — 发票列表页
- `InvoiceDetailView.vue` — 发票详情页
- `InvoiceFormDialog.vue` — 创建/编辑发票对话框

### 3.5 ContextPanel — `components/panels/InvoicePanel.vue`

## 4. Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 发票头 | `mantle.account.invoice.Invoice` |
| 发票项 | `mantle.account.invoice.InvoiceItem` |
| 付款 | `mantle.account.payment.Payment` |

## 5. 测试策略
- 后端: JUnit 5 + jqwik 属性测试
- 前端: Vitest 单元测试

## 6. 导航集成（⚠️ 暂不执行，由 Kiro 统一处理）
- App.vue navItems 添加 invoice 项（icon: Receipt）
- navigation.ts 添加 invoice NavId
- ContextPanel 注册 InvoicePanel
- router/index.js 添加 /invoice, /invoice/:invoiceId 路由
- icons.js 导入 Receipt 图标
