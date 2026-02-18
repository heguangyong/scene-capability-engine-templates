---
name: deep-dive-verification
category: other
description: Template for Deep Dive Verification
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document

## Introduction

本 Spec 旨在深度调研验证 Moqui ERP 系统中可能被遗漏的功能。在 02-02 需求映射分析中，我们使用关键词匹配方法标记了大量功能为"未覆盖"（功能覆盖率 28.18%）。然而，这些功能可能实际上是存在的，只是我们的识别方法不够深入。

本次深度调研将采用更系统的方法，通过分析实体定义、基础数据、服务定义和界面定义，来验证这些功能的实际存在性，从而得出更准确的功能覆盖率评估。

## Glossary

- **System**: Moqui ERP 系统及其源代码
- **Analyzer**: 执行深度调研的分析工具或分析人员
- **Entity_Definition**: Moqui 框架中的实体定义文件（XML 格式）
- **Service_Definition**: Moqui 框架中的服务定义文件（XML 格式）
- **Screen_Definition**: Moqui 框架中的界面定义文件（XML 格式）
- **Base_Data**: Moqui 框架中的基础数据配置文件（XML 格式）
- **Coverage_Matrix**: 功能覆盖率矩阵，记录需求功能与系统功能的映射关系
- **Party_Entity**: Moqui 中的当事人实体，用于管理客户、供应商、组织等
- **WorkEffort_Entity**: Moqui 中的工作任务实体，用于管理作业、项目等
- **Verification_Report**: 功能验证报告，记录每个功能的验证结果
- **Missed_Function**: 在初次分析中被遗漏但实际存在的功能

## Requirements

### Requirement 1: 主数据管理功能验证

**User Story:** 作为系统分析师，我需要验证主数据管理相关功能的实际存在性，以便准确评估系统的功能覆盖率。

#### Acceptance Criteria

1. WHEN 分析客商主数据维护功能时，THE Analyzer SHALL 搜索 Party 实体及其关系定义，验证客户、供应商、联系人等主数据管理能力
2. WHEN 分析作业价格维护功能时，THE Analyzer SHALL 搜索 WorkEffort 相关实体和服务，验证工作类型、单价、费率等价格管理能力
3. WHEN 分析计量单位管理功能时，THE Analyzer SHALL 搜索枚举表定义和基础数据，验证计量单位的完整性（应包含百余个单位）
4. WHEN 分析数据表结构查询功能时，THE Analyzer SHALL 搜索工具分类中的数据管理界面和服务，验证元数据查询能力
5. WHEN 分析电子文件归档功能时，THE Analyzer SHALL 搜索维基知识库功能和 MinIO 集成，验证文档管理和归档能力

### Requirement 2: 组织和财务主数据验证

**User Story:** 作为系统分析师，我需要验证组织管理和财务主数据相关功能，以便完善功能覆盖率评估。

#### Acceptance Criteria

1. WHEN 分析组织管理功能时，THE Analyzer SHALL 搜索 Party 组织结构定义，验证组织层级、部门、岗位等管理能力
2. WHEN 分析财务主数据功能时，THE Analyzer SHALL 搜索 GlAccount 相关实体和服务，验证会计科目、成本中心等财务主数据管理能力
3. WHEN 分析价格主数据功能时，THE Analyzer SHALL 搜索 ProductPrice 相关实体和服务，验证产品定价、价格策略等管理能力

### Requirement 3: 系统化调研方法

**User Story:** 作为系统分析师，我需要采用系统化的调研方法，以便全面准确地识别系统功能。

#### Acceptance Criteria

1. WHEN 调研某个功能时，THE Analyzer SHALL 按照"实体定义 → 基础数据 → 服务定义 → 界面定义"的顺序进行分析
2. WHEN 搜索实体定义时，THE Analyzer SHALL 使用 grepSearch 工具在 `**/*.xml` 文件中搜索相关实体名称和关系
3. WHEN 搜索基础数据时，THE Analyzer SHALL 在 `framework/data/*.xml` 和 `runtime/component/*/data/*.xml` 中查找预置数据
4. WHEN 搜索服务定义时，THE Analyzer SHALL 在 `**/*Services.xml` 文件中查找相关服务方法
5. WHEN 搜索界面定义时，THE Analyzer SHALL 在 `**/*Screens.xml` 文件中查找相关界面配置
6. WHEN 完成某个功能的调研时，THE Analyzer SHALL 记录验证结果，包括功能是否存在、证据文件路径、功能完整性评估

### Requirement 4: 验证报告生成

**User Story:** 作为项目经理，我需要获得详细的功能验证报告，以便了解系统的真实功能覆盖情况。

#### Acceptance Criteria

1. WHEN 完成所有功能验证时，THE System SHALL 生成详细的验证报告，包含每个功能的验证结果
2. WHEN 生成验证报告时，THE System SHALL 包含以下信息：功能名称、验证状态（存在/不存在）、证据文件、功能完整性评分（0-10）、说明
3. WHEN 识别出被遗漏的功能时，THE System SHALL 在报告中单独列出 Missed_Function 清单，说明为何初次分析遗漏
4. WHEN 生成验证报告时，THE System SHALL 提供功能分类统计，按主数据、业务流程、系统管理等维度汇总

### Requirement 5: 功能覆盖率矩阵更新

**User Story:** 作为系统分析师，我需要更新功能覆盖率矩阵，以便反映真实的功能覆盖情况。

#### Acceptance Criteria

1. WHEN 验证发现某个功能实际存在时，THE System SHALL 更新 Coverage_Matrix，将该功能状态从"未覆盖"改为"已覆盖"
2. WHEN 更新 Coverage_Matrix 时，THE System SHALL 记录功能的实现方式、相关实体、相关服务、相关界面
3. WHEN 完成所有更新时，THE System SHALL 重新计算功能覆盖率，生成修正后的覆盖率报告
4. WHEN 生成修正后的覆盖率报告时，THE System SHALL 对比修正前后的覆盖率变化，说明提升幅度和原因

### Requirement 6: 调研结果归档

**User Story:** 作为系统分析师，我需要将调研结果归档到 Spec 目录，以便后续参考和追溯。

#### Acceptance Criteria

1. WHEN 完成功能验证时，THE System SHALL 将验证报告保存到 `.kiro/specs/{{SPEC_NAME}}/reports/` 目录
2. WHEN 生成更新后的覆盖率矩阵时，THE System SHALL 将矩阵文件保存到 `.kiro/specs/{{SPEC_NAME}}/results/` 目录
3. WHEN 发现重要证据文件时，THE System SHALL 记录文件路径和关键内容摘要，保存到 `.kiro/specs/{{SPEC_NAME}}/diagnostics/` 目录
4. WHEN 完成所有调研工作时，THE System SHALL 生成调研总结文档，包含方法论、发现、结论和建议

### Requirement 7: 质量保证

**User Story:** 作为质量保证人员，我需要确保调研结果的准确性和完整性，以便为后续决策提供可靠依据。

#### Acceptance Criteria

1. WHEN 标记某个功能为"存在"时，THE Analyzer SHALL 提供至少两种类型的证据（如实体定义 + 界面定义）
2. WHEN 评估功能完整性时，THE Analyzer SHALL 使用 0-10 评分标准，并说明评分依据
3. WHEN 发现功能部分存在时，THE Analyzer SHALL 明确说明哪些部分存在、哪些部分缺失
4. WHEN 完成验证报告时，THE Analyzer SHALL 进行自查，确保所有重点功能都已验证，无遗漏

## Priority Focus Areas

基于 02-02 分析结果，以下功能领域应优先验证（这些领域标记了大量"未覆盖"功能）：

1. **主数据管理** (优先级：最高)
   - 客商主数据维护
   - 作业价格维护
   - 计量单位管理
   - 数据表结构查询
   - 电子文件归档

2. **组织和人力资源** (优先级：高)
   - 组织结构管理
   - 部门管理
   - 岗位管理
   - 员工信息管理

3. **财务管理** (优先级：高)
   - 会计科目管理
   - 成本中心管理
   - 预算管理
   - 财务报表

4. **供应链管理** (优先级：中)
   - 采购管理
   - 库存管理
   - 销售管理
   - 物流管理

## Success Criteria

本 Spec 成功的标志：

1. 完成至少 50 个重点功能的深度验证
2. 功能覆盖率从 28.18% 提升至至少 40%
3. 识别出至少 20 个被遗漏的功能
4. 生成完整的验证报告和更新后的覆盖率矩阵
5. 所有验证结果都有充分的证据支持
