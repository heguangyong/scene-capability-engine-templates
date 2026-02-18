---
name: requirement-mapping
category: other
description: Template for Requirement Mapping
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design: {{SPEC_NAME}}

**子 Spec**: 需求映射分析  
**主 Spec**: 02-00-source-code-analysis-overview  
**创建日期**: {{DATE}}

---

## Overview

本设计文档定义需求映射分析的技术方案、算法设计和实现策略。核心目标是建立 Moqui 能力与客户需求之间的映射关系，计算覆盖度，识别功能缺口。

---

## Architecture

### 数据流

```
┌─────────────────────────────────────────────────────────┐
│  Input 1: Customer Requirements (111 items)            │
│  Source: 01-00-customer-requirements/requirements.md   │
└─────────────────┬───────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────┐
│  Input 2: Moqui Capabilities                            │
│  Source: 02-01-basic-analysis/                          │
│    - 674 Entities                                       │
│    - 795 Services                                       │
│    - 406 Screens                                        │
└─────────────────┬───────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────┐
│  Processing: Mapping Algorithm                          │
│    1. Extract keywords from requirements                │
│    2. Extract keywords from capabilities                │
│    3. Calculate similarity scores                       │
│    4. Generate mapping matrix                           │
│    5. Calculate coverage rates                          │
└─────────────────┬───────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────┐
│  Output 1: Coverage Matrix (JSON/CSV)                   │
│  Output 2: Functional Gaps (JSON)                       │
│  Output 3: Analysis Report (Markdown)                   │
└─────────────────────────────────────────────────────────┘
```

---

## Mapping Algorithm Design

### Phase 1: 关键词提取

**需求关键词提取**:
```python
def extract_requirement_keywords(requirement_text):
    """
    从需求文本中提取关键词
    
    策略:
    1. 业务实体: Material, BOM, Routing, Order, Invoice, etc.
    2. 业务操作: Create, Modify, Delete, Calculate, Generate, etc.
    3. 业务属性: Status, Type, Quantity, Price, Date, etc.
    """
    keywords = {
        'entities': [],    # 实体关键词
        'operations': [],  # 操作关键词
        'attributes': []   # 属性关键词
    }
    return keywords
```

**Moqui 能力关键词提取**:
```python
def extract_capability_keywords(entity_name, service_name, screen_name):
    """
    从 Moqui 能力中提取关键词
    
    策略:
    1. 实体名: Product, Order, Invoice, Party, etc.
    2. 服务名: create#Product, update#Order, calculate#Price, etc.
    3. 界面名: ProductDetail, OrderList, InvoiceForm, etc.
    """
    keywords = {
        'entities': [],
        'operations': [],
        'attributes': []
    }
    return keywords
```

### Phase 2: 相似度计算

**匹配策略**:

1. **精确匹配** (权重: 1.0)
   - 需求关键词与 Moqui 能力关键词完全一致
   - 示例: "Material" ↔ "Product" (同义词)

2. **部分匹配** (权重: 0.7)
   - 需求关键词包含在 Moqui 能力关键词中
   - 示例: "Sales Order" ↔ "OrderHeader"

3. **语义匹配** (权重: 0.5)
   - 需求关键词与 Moqui 能力关键词语义相关
   - 示例: "Purchase" ↔ "Procurement"

**相似度计算公式**:
```
Similarity = (Σ(matched_keywords × weight)) / total_keywords
Coverage_Rate = Similarity × 100%
```

### Phase 3: 覆盖度评分

**评分规则**:

| 覆盖率 | 等级 | 说明 |
|--------|------|------|
| 90-100% | 完全覆盖 | 需求的所有关键功能都有对应的 Moqui 能力 |
| 70-89% | 高度覆盖 | 需求的大部分功能有对应的 Moqui 能力，少量需要定制 |
| 50-69% | 部分覆盖 | 需求的部分功能有对应的 Moqui 能力，需要较多定制 |
| 30-49% | 低度覆盖 | 需求的少量功能有对应的 Moqui 能力，需要大量定制 |
| 1-29% | 极低覆盖 | 需求几乎没有对应的 Moqui 能力，需要全新开发 |
| 0% | 未覆盖 | 需求完全没有对应的 Moqui 能力 |

---

## Data Structures

### 需求映射矩阵

```json
{
  "requirement_id": "Requirement 1",
  "requirement_title": "Master Data Management",
  "domain": "Master Data",
  "acceptance_criteria": [
    {
      "criteria_id": "1.1",
      "criteria_text": "Material master data creation...",
      "mapped_capabilities": [
        {
          "type": "entity",
          "name": "Product",
          "component": "mantle-udm",
          "match_score": 0.95
        },
        {
          "type": "service",
          "name": "create#Product",
          "component": "mantle-usl",
          "match_score": 0.90
        }
      ],
      "coverage_rate": 92.5,
      "coverage_level": "完全覆盖"
    }
  ],
  "overall_coverage_rate": 85.0,
  "functional_gaps": [
    {
      "gap_id": "GAP-001",
      "description": "Material codes exceeding 18 characters",
      "severity": "Medium",
      "effort": "Low"
    }
  ]
}
```

### 覆盖度统计

```json
{
  "overall_statistics": {
    "total_requirements": 111,
    "total_criteria": 450,
    "average_coverage_rate": 72.5,
    "fully_covered": 45,
    "highly_covered": 30,
    "partially_covered": 25,
    "lowly_covered": 8,
    "not_covered": 3
  },
  "domain_statistics": [
    {
      "domain": "Master Data Management",
      "requirements_count": 15,
      "coverage_rate": 85.0,
      "coverage_level": "高度覆盖"
    }
  ]
}
```

### 功能缺口清单

```json
{
  "functional_gaps": [
    {
      "gap_id": "GAP-001",
      "requirement_id": "Requirement 1",
      "criteria_id": "1.1",
      "description": "Material codes exceeding 18 characters",
      "current_capability": "Moqui supports standard material codes",
      "gap_detail": "Need to extend material code field length",
      "severity": "Medium",
      "effort": "Low",
      "priority": "High",
      "recommendation": "Extend Product.productId field to support longer codes"
    }
  ]
}
```

---

## Implementation Strategy

### 脚本设计

**map_requirements.py**:
```python
#!/usr/bin/env python3
"""
需求映射分析脚本

功能:
1. 加载客户需求文档
2. 加载 Moqui 能力数据
3. 执行关键词匹配
4. 计算覆盖度
5. 生成映射矩阵
6. 识别功能缺口
7. 生成分析报告
"""

import json
import re
from pathlib import Path
from typing import Dict, List, Tuple

# 关键词字典
KEYWORD_MAPPINGS = {
    'Material': ['Product', 'Item', 'Asset'],
    'BOM': ['ProductAssoc', 'BillOfMaterial'],
    'Routing': ['WorkEffort', 'ProductionRun'],
    'Order': ['OrderHeader', 'OrderItem'],
    'Invoice': ['Invoice', 'InvoiceItem'],
    'Purchase': ['PurchaseOrder', 'Procurement'],
    'Sales': ['SalesOrder', 'SalesInvoice'],
    'Inventory': ['InventoryItem', 'AssetReservation'],
    'Production': ['WorkEffort', 'ProductionRun'],
    'Cost': ['CostComponent', 'AcctgTrans'],
    'Project': ['Project', 'WorkEffort'],
    'Equipment': ['FixedAsset', 'Equipment'],
    'Service': ['ServiceRequest', 'WorkEffort']
}

def load_requirements(requirements_file: Path) -> List[Dict]:
    """加载客户需求"""
    pass

def load_capabilities(capabilities_file: Path) -> Dict:
    """加载 Moqui 能力"""
    pass

def extract_keywords(text: str) -> List[str]:
    """提取关键词"""
    pass

def calculate_similarity(req_keywords: List[str], 
                         cap_keywords: List[str]) -> float:
    """计算相似度"""
    pass

def map_requirements_to_capabilities(requirements: List[Dict],
                                     capabilities: Dict) -> Dict:
    """映射需求到能力"""
    pass

def calculate_coverage_rate(mapping: Dict) -> float:
    """计算覆盖率"""
    pass

def identify_functional_gaps(mapping: Dict) -> List[Dict]:
    """识别功能缺口"""
    pass

def generate_report(mapping: Dict, 
                    coverage: Dict, 
                    gaps: List[Dict],
                    output_file: Path):
    """生成分析报告"""
    pass

def main():
    """主函数"""
    # 1. 加载数据
    requirements = load_requirements(...)
    capabilities = load_capabilities(...)
    
    # 2. 执行映射
    mapping = map_requirements_to_capabilities(requirements, capabilities)
    
    # 3. 计算覆盖度
    coverage = calculate_coverage_rate(mapping)
    
    # 4. 识别缺口
    gaps = identify_functional_gaps(mapping)
    
    # 5. 生成报告
    generate_report(mapping, coverage, gaps, ...)

if __name__ == '__main__':
    main()
```

---

## Report Structure

### 功能覆盖矩阵报告

```markdown
# Functional Coverage Matrix Report

**Generated**: {{DATE}}
**Spec**: {{SPEC_NAME}}
**Version**: 1.0

---

## Executive Summary

### Key Findings
- 总体功能覆盖率: 72.5%
- 完全覆盖需求: 45/111 (40.5%)
- 高度覆盖需求: 30/111 (27.0%)
- 部分覆盖需求: 25/111 (22.5%)
- 低度覆盖需求: 8/111 (7.2%)
- 未覆盖需求: 3/111 (2.7%)

### Coverage by Domain
[18 个需求领域的覆盖率统计]

### Critical Gaps
[关键功能缺口列表]

---

## Detailed Mapping Matrix

### Requirement 1: Master Data Management
**Overall Coverage**: 85.0%

#### Acceptance Criteria 1.1
**Text**: Material master data creation...
**Coverage**: 92.5%
**Mapped Capabilities**:
- Entity: Product (mantle-udm) - 95% match
- Service: create#Product (mantle-usl) - 90% match
- Screen: ProductDetail (SimpleScreens) - 90% match

**Gaps**:
- GAP-001: Material codes exceeding 18 characters

[重复 111 项需求]

---

## Coverage Statistics

### Overall Statistics
[总体统计图表]

### Domain Statistics
[领域统计图表]

---

## Functional Gaps

### High Priority Gaps
[高优先级缺口列表]

### Medium Priority Gaps
[中优先级缺口列表]

### Low Priority Gaps
[低优先级缺口列表]

---

## Recommendations

### Immediate Actions
[立即行动建议]

### Short-term Actions
[短期行动建议]

### Long-term Actions
[长期行动建议]

---

## Appendix

### Mapping Methodology
[映射方法说明]

### Keyword Dictionary
[关键词字典]

### Data Sources
[数据来源]
```

---

## Quality Assurance

### 验证检查点

1. **数据完整性**:
   - ✅ 所有 111 项需求都有映射结果
   - ✅ 所有 18 个需求领域都有覆盖率计算
   - ✅ 所有功能缺口都有详细说明

2. **准确性**:
   - ✅ 关键词匹配准确率 > 90%
   - ✅ 覆盖率评分合理（人工抽查 20%）
   - ✅ 功能缺口识别准确（人工审核）

3. **可用性**:
   - ✅ 报告清晰易懂
   - ✅ 建议可执行
   - ✅ 数据可追溯

### 人工审核

**审核范围**:
- 高覆盖率需求（> 80%）: 抽查 10%
- 中覆盖率需求（50-80%）: 抽查 20%
- 低覆盖率需求（< 50%）: 全部审核
- 未覆盖需求（0%）: 全部审核

---

## Risk Management

### 已识别风险

1. **关键词匹配不准确**
   - 风险: 自动匹配可能误判
   - 缓解: 人工审核 + 关键词字典优化

2. **覆盖率评分主观性**
   - 风险: 评分标准可能不一致
   - 缓解: 明确评分规则 + 人工校准

3. **功能缺口遗漏**
   - 风险: 可能遗漏隐含的功能缺口
   - 缓解: 多轮审核 + 专家评审

---

## Success Criteria

1. ✅ 映射矩阵完整（111 项需求 × Moqui 能力）
2. ✅ 覆盖率计算准确（18 个领域 + 总体）
3. ✅ 功能缺口清单完整（包含优先级）
4. ✅ 报告清晰可读（支持决策）
5. ✅ 数据可追溯（提供来源）

---

**版本**: v1.0  
**状态**: Active  
**预计完成**: 2026-01-31
