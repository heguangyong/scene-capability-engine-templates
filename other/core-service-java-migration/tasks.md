---
name: core-service-java-migration
category: other
description: Template for Core Service Java Migration
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Tasks: 03-02 {{SPEC_NAME_TITLE}}

## 任务列表

- [x] 1 固化 Java 迁移模板
  - [x] 1.1 建立服务实现模板（日志、事务、异常）
  - [x] 1.2 编写编码规范与反模式清单
  - [x] 1.3 产出 `docs/java-migration-guide.md`
  - _Validates: Requirements 3.2_

- [x] 2 执行首批核心服务迁移
  - [x] 2.1 按优先级选择首批服务（20%-30%）
  - [x] 2.2 完成 Java 实现并保留 XML 契约
  - [x] 2.3 形成迁移追踪清单
  - _Validates: Requirements 3.1_

- [x] 3 建立验证闭环
  - [x] 3.1 为首批服务补齐单测/集成测试
  - [x] 3.2 运行迁移前后性能对比
  - [x] 3.3 产出 `reports/core-migration-validation.md`
  - _Validates: Requirements 3.3_

- [x] 4 输出批量迁移输入
  - [x] 4.1 总结可复用迁移模式
  - [x] 4.2 标注剩余服务的分批策略
  - [x] 4.3 产出 `results/batch-migration-input.md`
  - _Validates: Requirements 3.1, 3.2, 3.3_
