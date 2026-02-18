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

# 财务管理模块 (Accounting / Invoice)

## 概述
实现 Moqui ERP 财务管理模块的 REST API 后端和 Vue 3 前端，支持发票和付款的基本管理。

---

## 用户故事与验收标准

### 1. 发票列表查询
**作为** ERP 操作员，**我想** 查看发票列表并支持分页和筛选，**以便** 快速找到目标发票。

**验收标准:**
- 1.1 GET `/api/v1/invoice` 返回分页发票列表（pageIndex, pageSize, totalCount, totalPages）
- 1.2 支持按 invoiceTypeEnumId 筛选（销售发票/采购发票）
- 1.3 支持按 statusId 筛选
- 1.4 支持按 searchKeyword 模糊搜索（invoiceId 或 description）
- 1.5 每条发票返回: invoiceId, invoiceTypeEnumId, fromPartyId, toPartyId, statusId, invoiceDate, currencyUomId, invoiceTotal
- 1.6 前端 InvoiceListView 展示发票表格

### 2. 发票详情查看
**作为** ERP 操作员，**我想** 查看发票的完整详情，**以便** 了解发票的所有信息。

**验收标准:**
- 2.1 GET `/api/v1/invoice/{invoiceId}` 返回发票头 + 发票项列表
- 2.2 发票项包含: invoiceItemSeqId, itemTypeEnumId, description, quantity, amount, productId
- 2.3 前端 InvoiceDetailView 展示发票头和发票项表格

### 3. 创建发票
**作为** ERP 操作员，**我想** 创建新发票，**以便** 记录交易。

**验收标准:**
- 3.1 POST `/api/v1/invoice` 创建发票头，必填: invoiceTypeEnumId, fromPartyId, toPartyId, currencyUomId
- 3.2 初始状态设为 InvoiceInProcess
- 3.3 自动设置 invoiceDate 为当前时间
- 3.4 返回新创建的 invoiceId
- 3.5 前端 InvoiceFormDialog 提供创建表单

### 4. 更新发票
**作为** ERP 操作员，**我想** 修改发票基本信息。

**验收标准:**
- 4.1 PUT `/api/v1/invoice/{invoiceId}` 更新发票头字段
- 4.2 仅更新请求中包含的字段
- 4.3 发票不存在时返回 404

### 5. 发票状态管理
**作为** ERP 操作员，**我想** 推进发票状态。

**验收标准:**
- 5.1 POST `/api/v1/invoice/{invoiceId}/update-status` 更新发票状态
- 5.2 支持的状态: InvoiceInProcess, InvoiceFinalized, InvoiceSent, InvoicePmtRecvd, InvoiceCancelled

### 6. 发票项管理
**作为** ERP 操作员，**我想** 管理发票中的明细项。

**验收标准:**
- 6.1 POST `/api/v1/invoice/{invoiceId}/items` 添加发票项
- 6.2 PUT `/api/v1/invoice/{invoiceId}/items/{invoiceItemSeqId}` 更新发票项
- 6.3 DELETE `/api/v1/invoice/{invoiceId}/items/{invoiceItemSeqId}` 删除发票项

### 7. 付款列表查询
**作为** ERP 操作员，**我想** 查看付款记录列表。

**验收标准:**
- 7.1 GET `/api/v1/payment` 返回分页付款列表
- 7.2 支持按 paymentTypeEnumId、statusId 筛选
- 7.3 每条付款返回: paymentId, paymentTypeEnumId, fromPartyId, toPartyId, amount, statusId, effectiveDate

---

## 非功能需求
- 遵循现有 PartyService/ProductService 的代码模式
- 后端使用 Moqui Entity API 操作数据
- 前端使用 Pinia Store + API Service 分层架构

## Moqui 实体映射
| 功能 | Moqui Entity |
|------|-------------|
| 发票头 | `mantle.account.invoice.Invoice` |
| 发票项 | `mantle.account.invoice.InvoiceItem` |
| 付款 | `mantle.account.payment.Payment` |
