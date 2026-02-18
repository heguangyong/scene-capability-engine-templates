---
name: basic-analysis
category: other
description: Template for Basic Analysis
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Requirements Document: 02-00-source-code-analysis

## Introduction

本 Spec 用于分析基于 Moqui Framework 的 ERP 项目源代码，评估其架构、技术栈、代码质量，并将其与客户需求（01-00-customer-requirements）进行映射对比。

## Glossary

- **Moqui_Framework**: 开源企业应用框架，基于 Java/Groovy
- **Source_Code**: 待分析的 Moqui ERP 项目源代码
- **Tech_Stack**: Moqui 3.1.0 + Vue.js 3 + Electron 28
- **Component**: Moqui 组件（mantle-udm, mantle-usl, SimpleScreens, MarbleERP 等）
- **Architecture_Pattern**: Moqui 的组件化架构模式
- **Feature_Coverage**: 代码实现的功能与客户需求文档的覆盖度
- **Domestic_Compatibility**: 代码对国产化环境（数据库、操作系统）的适配程度

## Requirements

### Requirement 1: Moqui 项目结构分析

**User Story:** 作为分析人员，我希望了解 Moqui 项目的完整结构，以便理解项目组织方式。

#### Acceptance Criteria

1. THE System SHALL 识别 Moqui Framework 版本（当前为 3.1.0，heguangyong fork）
2. THE System SHALL 识别所有 runtime 组件（mantle-udm, mantle-usl, SimpleScreens, MarbleERP, HiveMind 等）
3. THE System SHALL 分析组件间的依赖关系
4. THE System SHALL 识别自定义 ERP 组件
5. THE System SHALL 生成项目结构可视化图表

### Requirement 2: 技术栈识别

**User Story:** 作为分析人员，我希望识别项目使用的完整技术栈，以便评估技术选型。

#### Acceptance Criteria

1. THE System SHALL 识别后端技术栈（Moqui 3.1.0, Java 21, Groovy, Gradle 8.x）
2. THE System SHALL 识别前端技术栈（Vue.js 3, Electron 28, Vite 5.0）
3. THE System SHALL 识别数据库配置（H2 开发环境, PostgreSQL/MySQL 生产环境）
4. THE System SHALL 识别搜索引擎（OpenSearch 2.4.0 / ElasticSearch 7.10.2）
5. THE System SHALL 识别所有第三方依赖和工具组件（moqui-fop, moqui-mcp, moqui-minio）
6. THE System SHALL 生成技术栈清单文档

### Requirement 3: Moqui 组件功能分析

**User Story:** 作为业务分析师，我希望分析每个 Moqui 组件提供的功能，以便映射到客户需求。

#### Acceptance Criteria

1. THE System SHALL 分析 mantle-udm（数据模型）提供的实体定义
2. THE System SHALL 分析 mantle-usl（服务层）提供的服务接口
3. THE System SHALL 分析 SimpleScreens 提供的界面功能
4. THE System SHALL 分析 MarbleERP 提供的 ERP 核心功能
5. THE System SHALL 分析 HiveMind 提供的项目管理功能
6. THE System SHALL 识别自定义组件的功能实现
7. THE System SHALL 生成组件功能清单

### Requirement 4: 功能映射到客户需求

**User Story:** 作为业务分析师，我希望将 Moqui 组件功能映射到客户需求文档，以便评估功能覆盖度。

#### Acceptance Criteria

1. THE System SHALL 将 Moqui 功能映射到客户需求（01-00-customer-requirements）的 18 个需求领域
2. THE System SHALL 识别已实现的功能（如主数据管理、生产计划、采购管理等）
3. THE System SHALL 识别未实现或部分实现的功能
4. THE System SHALL 评估每个需求领域的实现完整度（百分比）
5. THE System SHALL 生成功能覆盖度矩阵报告

### Requirement 5: 架构合理性评估

**User Story:** 作为架构师，我希望评估 Moqui 架构是否满足客户需求，以便识别架构改进点。

#### Acceptance Criteria

1. THE System SHALL 评估 Moqui 组件化架构是否支持微服务部署（需求 12.5）
2. THE System SHALL 评估是否支持集群部署（需求 12.3）
3. THE System SHALL 评估是否支持蓝绿部署（需求 12.4）
4. THE System SHALL 评估 API 开放性和集成能力（需求 12.10, 12.11）
5. THE System SHALL 评估安全保密功能（需求 12.12, 12.13）
6. THE System SHALL 生成架构评估报告

### Requirement 6: 国产化适配分析

**User Story:** 作为合规官，我希望评估 Moqui 项目的国产化适配程度，以便了解改造工作量。

#### Acceptance Criteria

1. THE System SHALL 分析数据库适配情况（H2, PostgreSQL, MySQL → 国产数据库）
2. THE System SHALL 分析 Java 21 在国产操作系统上的兼容性
3. THE System SHALL 识别第三方依赖的国产化替代方案
4. THE System SHALL 评估 OpenSearch/ElasticSearch 的国产化替代方案
5. THE System SHALL 评估前端 Electron 应用的国产化适配
6. THE System SHALL 估算国产化改造工作量
7. THE System SHALL 生成国产化适配评估报告

### Requirement 7: 代码质量评估

**User Story:** 作为质量工程师，我希望评估 Moqui 项目的代码质量，以便识别潜在问题。

#### Acceptance Criteria

1. THE System SHALL 评估 Moqui 组件的代码规范性
2. THE System SHALL 评估实体定义（XML）的完整性
3. THE System SHALL 评估服务定义（XML/Groovy）的质量
4. THE System SHALL 评估界面定义（XML）的可维护性
5. THE System SHALL 识别潜在的性能问题
6. THE System SHALL 生成代码质量报告

### Requirement 8: 生成实施建议

**User Story:** 作为项目经理，我希望获得基于分析的实施建议，以便指导后续开发工作。

#### Acceptance Criteria

1. THE System SHALL 基于功能覆盖度分析生成缺失功能清单
2. THE System SHALL 基于架构评估生成架构改进建议
3. THE System SHALL 基于国产化分析生成适配改造计划
4. THE System SHALL 按优先级排序所有改进建议
5. THE System SHALL 估算每项改进的工作量
6. THE System SHALL 生成实施路线图
7. THE System SHALL 生成完整的实施建议文档

## Non-Functional Requirements

### Performance
- 代码扫描应在合理时间内完成（取决于代码库大小）
- 支持增量分析（只分析变更的组件）

### Accuracy
- 技术栈识别准确率应达到 95% 以上
- 功能映射准确率应达到 90% 以上
- 组件依赖关系识别准确率应达到 95% 以上

### Usability
- 分析报告应清晰易读
- 提供可视化图表辅助理解
- 生成可执行的改进建议

---

**注意**: 本 Spec 的 design.md 和 tasks.md 将基于 Moqui 项目特性生成。
