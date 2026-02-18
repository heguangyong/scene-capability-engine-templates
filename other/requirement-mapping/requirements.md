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

# Requirements: {{SPEC_NAME}}

**子 Spec**: 需求映射分析  
**主 Spec**: 02-00-source-code-analysis-overview  
**创建日期**: {{DATE}}  
**项目**: 331-poc

---

## Introduction

本子 Spec 负责将 Moqui Framework ERP 系统的能力映射到客户的 111 项功能需求，计算功能覆盖度，识别功能缺口，并提供优先级建议。这是评估 Moqui 系统是否满足客户需求的关键分析阶段。

## Glossary

- **Requirement_Domain**: 需求领域，18 个高层业务领域（如主数据管理、销售订单管理等）
- **Functional_Requirement**: 功能需求，111 项具体的业务需求
- **Moqui_Capability**: Moqui 能力，从 02-01 分析得到的实体、服务、界面
- **Coverage_Rate**: 覆盖率，Moqui 能力对需求的满足程度（百分比）
- **Functional_Gap**: 功能缺口，Moqui 未覆盖或部分覆盖的需求
- **Mapping_Matrix**: 映射矩阵，需求与 Moqui 能力的对应关系表

## Requirements

### Requirement 1: 需求映射分析

**User Story:** 作为业务分析师，我希望了解 Moqui 系统对每项客户需求的覆盖情况，以便评估系统的适用性。

#### Acceptance Criteria

1. THE Analysis SHALL 加载 111 项客户需求（来自 01-00-customer-requirements）
2. THE Analysis SHALL 加载 Moqui 能力目录（来自 02-01-basic-analysis）
3. THE Analysis SHALL 将每项需求映射到相关的 Moqui 实体、服务、界面
4. THE Analysis SHALL 使用关键词匹配算法识别需求与能力的关联
5. THE Analysis SHALL 为每项需求计算覆盖度评分（0-100%）
6. THE Analysis SHALL 生成需求映射矩阵（Requirement × Moqui Capability）

### Requirement 2: 覆盖度计算

**User Story:** 作为项目经理，我希望了解 Moqui 系统的整体功能覆盖度，以便评估实施风险。

#### Acceptance Criteria

1. THE Analysis SHALL 计算每个需求领域的覆盖率（18 个领域）
2. THE Analysis SHALL 计算总体功能覆盖率（所有 111 项需求）
3. THE Analysis SHALL 识别高覆盖率领域（> 80%）
4. THE Analysis SHALL 识别中覆盖率领域（50-80%）
5. THE Analysis SHALL 识别低覆盖率领域（< 50%）
6. THE Analysis SHALL 生成覆盖度统计图表

### Requirement 3: 功能缺口识别

**User Story:** 作为技术架构师，我希望识别 Moqui 系统的功能缺口，以便规划定制开发工作。

#### Acceptance Criteria

1. THE Analysis SHALL 识别完全未覆盖的需求（覆盖率 = 0%）
2. THE Analysis SHALL 识别部分覆盖的需求（0% < 覆盖率 < 100%）
3. THE Analysis SHALL 为每个功能缺口提供详细说明
4. THE Analysis SHALL 估算每个功能缺口的开发工作量（高/中/低）
5. THE Analysis SHALL 生成功能缺口清单

### Requirement 4: 优先级建议

**User Story:** 作为项目经理，我希望获得功能缺口的优先级建议，以便制定实施计划。

#### Acceptance Criteria

1. THE Analysis SHALL 根据业务重要性对功能缺口排序
2. THE Analysis SHALL 根据开发工作量对功能缺口排序
3. THE Analysis SHALL 生成优先级矩阵（重要性 × 工作量）
4. THE Analysis SHALL 推荐高优先级功能缺口（高重要性 + 低工作量）
5. THE Analysis SHALL 推荐低优先级功能缺口（低重要性 + 高工作量）

### Requirement 5: 报告生成

**User Story:** 作为干系人，我希望获得清晰的功能覆盖度报告，以便做出决策。

#### Acceptance Criteria

1. THE Analysis SHALL 生成功能覆盖矩阵报告（Markdown 格式）
2. THE Report SHALL 包含执行总结（关键发现、覆盖率、缺口）
3. THE Report SHALL 包含详细映射表（需求 × Moqui 能力）
4. THE Report SHALL 包含覆盖度统计图表
5. THE Report SHALL 包含功能缺口清单和优先级建议
6. THE Report SHALL 包含可执行的行动计划

---

## Mapping Methodology

### 关键词匹配算法

**步骤**:
1. 从需求文本中提取关键词（业务术语、实体名、操作动词）
2. 从 Moqui 能力中提取关键词（实体名、服务名、界面名）
3. 计算关键词相似度（精确匹配、部分匹配、语义匹配）
4. 根据匹配度计算覆盖率评分

**评分规则**:
- **完全覆盖** (100%): 需求的所有关键功能都有对应的 Moqui 能力
- **高度覆盖** (70-99%): 需求的大部分功能有对应的 Moqui 能力
- **部分覆盖** (30-69%): 需求的部分功能有对应的 Moqui 能力
- **低度覆盖** (1-29%): 需求的少量功能有对应的 Moqui 能力
- **未覆盖** (0%): 需求没有对应的 Moqui 能力

### 人工审核

**审核点**:
- 自动匹配结果的准确性
- 覆盖率评分的合理性
- 功能缺口的完整性
- 优先级建议的可行性

---

## Deliverables

### 数据产物

1. **coverage_matrix.json**: 需求映射矩阵（JSON 格式）
2. **coverage_statistics.csv**: 覆盖度统计（CSV 格式）
3. **functional_gaps.json**: 功能缺口清单（JSON 格式）

### 报告产物

1. **04-functional-coverage-matrix.md**: 功能覆盖矩阵报告

### 脚本产物

1. **map_requirements.py**: 需求映射分析脚本

---

## Success Criteria

1. ✅ 所有 111 项需求都有映射结果
2. ✅ 所有 18 个需求领域都有覆盖率计算
3. ✅ 功能缺口清单完整准确
4. ✅ 优先级建议可执行
5. ✅ 报告清晰易懂，支持决策

---

## Dependencies

**输入依赖**:
- 02-01-basic-analysis 的组件能力数据
- 01-00-customer-requirements 的需求文档

**输出供给**:
- 02-05-quality-and-recommendations 的缺失功能清单

---

**版本**: v1.0  
**状态**: Active  
**预计完成**: 2026-01-31
