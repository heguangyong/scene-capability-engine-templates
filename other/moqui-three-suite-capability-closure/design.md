---
name: moqui-three-suite-capability-closure
category: other
description: Template for Moqui Three Suite Capability Closure
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# 设计文档：Moqui 三大应用能力闭环（64-00）

## 1. 设计目标

在不推翻现有领域化导航（制造/供应链/资产/财务/销售/组织/人力/治理平台）的前提下，建立“应用语义层”：

1. `HiveMind`：项目与协同驱动（项目/WBS/审批/协作）  
2. `PopCommerce`：交易履约驱动（商品/订单/发运/退货）  
3. `MarbleERP`：企业运营驱动（计划/供应链/资产/核算/治理）

---

## 2. 输入来源

- Moqui 三大应用来源：`02-00`、`02-01`、`02-02` 分析产物  
- 当前能力闭环：`62-01` 能力矩阵（`Full=169 / Partial=0 / Missing=0 / OutOfScope=3`）  
- 当前导航组织：`frontend/src/renderer/config/menu-config.js`  
- 当前守护链路：`63-00~63-04`

---

## 3. 设计方案

### 3.1 双层组织模型

- **领域层（已落地）**：当前菜单的一级领域入口，负责执行效率。  
- **应用层（本 Spec 补齐）**：三大应用语义映射，负责业务认知一致性。  

### 3.2 三层 ontology 对齐模型

每个应用输出统一三层结构：

1. **实体关系**：主对象与跨对象关系（如 `Order -> Shipment -> Invoice`）  
2. **业务规则**：状态机、校验、审批、审计约束  
3. **决策逻辑**：路由、策略分支、异常回退、跨域触发

### 3.3 输出资产

- 映射基线：`docs/moqui-three-suite-capability-map.md`  
- scene 合同：`docs/scene.yaml`、`docs/scene-package.json`  
- 治理回填：`CURRENT_CONTEXT` 与 `handoff-manifest`

---

## 4. 执行策略

1. 先固化基线映射与待增强项  
2. 再以 scene 合同沉淀 KSE 可内化资产  
3. 接入 63 系列守护，避免后续偏移

---

## 5. 完成定义（DoD）

1. `64-00` 文档结构完整（requirements/design/tasks/docs）  
2. `scene` 严格校验通过  
3. `status/doctor` 通过  
4. 上下文与 handoff 清单完成回填

