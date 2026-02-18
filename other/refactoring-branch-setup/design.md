---
name: refactoring-branch-setup
category: other
description: Template for Refactoring Branch Setup
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Moqui 重构分支设置 - 设计文档

**Spec ID**: {{SPEC_NAME}}  
**创建日期**: 2026-02-01  
**状态**: Approved

---

## 1. 设计概述

### 1.1 目标

建立一个清晰的基线和分支结构，为 Moqui 系统的重大重构工作提供安全的起点和明确的路线图。

### 1.2 设计原则

1. **安全第一**: 保留完整的基线，确保可以随时回退
2. **文档驱动**: 详细记录当前状态，为重构提供参考
3. **分阶段推进**: 将大型重构分解为可管理的阶段
4. **持续验证**: 每个阶段都有明确的验收标准

---

## 2. 架构设计

### 2.1 分支策略

```
master (main)
├── baseline-v1.0 (tag) ← 当前稳定状态
│
└── refactor/java-maven-jwt (branch) ← 重构工作分支
    ├── Phase 1: Preparation
    ├── Phase 2: Maven Migration
    ├── Phase 3: Groovy to Java
    ├── Phase 4: Frontend Separation
    ├── Phase 5: JWT Authentication
    └── Phase 6: Testing & Optimization
```

**分支保护规则**:
- `master`: 只接受经过审查的 PR
- `refactor/java-maven-jwt`: 允许直接推送（初期），后期改为 PR only

### 2.2 文档结构

```
.kiro/specs/{{SPEC_NAME}}/
├── requirements.md              # 需求文档
├── design.md                    # 设计文档（本文件）
├── tasks.md                     # 任务列表
├── BASELINE_SNAPSHOT.md         # 基线快照
├── BRANCHING_STRATEGY.md        # 分支策略
├── REFACTORING_ROADMAP.md       # 重构路线图
├── scripts/
│   ├── setup-refactor-branch.bat      # 分支设置脚本
│   ├── collect-baseline-metrics.bat   # 收集基线指标
│   └── verify-baseline.bat            # 验证基线
└── results/
    ├── baseline-metrics.json          # 基线指标数据
    ├── code-analysis.md               # 代码分析报告
    └── dependency-tree.txt            # 依赖树
```

---

## 3. 组件设计

### 3.1 Git 分支管理

#### 3.1.1 标签创建

**标签名称**: `baseline-v1.0`

**标签信息**:
```
Tag: baseline-v1.0
Date: 2026-02-01
Message: Baseline before major refactoring

System Status:
- Moqui Framework 3.1.0-rc2
- All components installed and working
- System starts in ~15 seconds
- All menus accessible
- No critical errors

Components:
- moqui-framework (core)
- tools (complete)
- webroot, HiveMind, MarbleERP, PopCommerce, SimpleScreens
- mantle-udm, mantle-usl, moqui-fop

Known Issues: All resolved
- Template files: Fixed
- Tools permissions: Fixed
- Component completeness: Verified

Refactoring Goals:
1. Remove all Groovy code → Pure Java
2. Migrate Gradle → Maven
3. Implement JWT authentication
4. Separate frontend and backend
```

#### 3.1.2 分支创建

**分支名称**: `refactor/java-maven-jwt`

**分支策略**:
- 从 `baseline-v1.0` 标签创建
- 定期合并 `master` 的 bug 修复
- 每个 Phase 完成后创建里程碑标签

**命名规范**:
- 里程碑标签: `refactor-m1`, `refactor-m2`, ...
- 功能分支: `refactor/feature-name`
- 修复分支: `refactor/fix-issue-name`

### 3.2 基线数据收集

#### 3.2.1 系统信息收集

**收集内容**:
1. **版本信息**
   - Moqui Framework 版本
   - Java 版本
   - Gradle 版本
   - 所有组件版本

2. **配置信息**
   - MoquiConf.xml 内容
   - MoquiDevConf.xml 内容
   - 数据库配置
   - 日志配置

3. **性能指标**
   - 启动时间（冷启动、热启动）
   - 内存占用（初始、峰值）
   - 响应时间（首页、登录、菜单）
   - 数据库大小

4. **代码统计**
   - 文件数量（按类型）
   - 代码行数（按语言）
   - Groovy 代码位置和数量
   - 依赖关系

**收集方法**:
```powershell
# 1. 版本信息
java -version
.\gradlew.bat --version

# 2. 启动时间
Measure-Command { .\gradlew.bat run }

# 3. 代码统计
Get-ChildItem -Recurse -Include *.java,*.groovy,*.xml | Measure-Object -Line

# 4. 依赖树
.\gradlew.bat dependencies > dependency-tree.txt
```

#### 3.2.2 代码分析

**分析维度**:
1. **Groovy 代码分布**
   - Service 层 Groovy 脚本
   - 工具类 Groovy 代码
   - 测试代码
   - 配置脚本

2. **依赖分析**
   - 直接依赖
   - 传递依赖
   - 版本冲突
   - 可选依赖

3. **架构分析**
   - 分层结构
   - 组件依赖
   - 循环依赖
   - 耦合度

**分析工具**:
- SonarQube（代码质量）
- JDepend（依赖分析）
- Gradle 内置工具

### 3.3 重构路线图

#### 3.3.1 Phase 1: 准备和分析（1-2 周）

**目标**: 全面了解系统，制定详细计划

**任务**:
1. 创建分支和标签
2. 收集基线数据
3. 分析 Groovy 代码使用情况
4. 设计 Maven 项目结构
5. 设计 JWT 认证架构
6. 设计前后端分离架构
7. 识别风险和制定缓解措施

**交付物**:
- 基线快照文档
- 代码分析报告
- Maven 项目结构设计
- JWT 认证设计文档
- 前后端分离架构设计
- 风险评估报告

#### 3.3.2 Phase 2: Maven 迁移（2-3 周）

**目标**: 将构建系统从 Gradle 迁移到 Maven

**任务**:
1. 创建 Maven 项目结构
2. 编写 pom.xml（父 POM 和子模块）
3. 迁移依赖管理
4. 配置 Maven 插件
5. 验证构建流程
6. 更新 CI/CD 配置

**交付物**:
- 完整的 Maven 项目结构
- 可工作的 pom.xml
- Maven 构建文档
- CI/CD 配置更新

#### 3.3.3 Phase 3: Groovy 到 Java 转换（4-6 周）

**目标**: 将所有 Groovy 代码转换为 Java

**任务**:
1. 识别所有 Groovy 代码
2. 按优先级排序（Service > 工具类 > 测试）
3. 逐个转换 Groovy 文件
4. 保持功能等价性
5. 编写单元测试
6. 验证功能正确性

**交付物**:
- 纯 Java 代码库
- 完整的单元测试
- 转换文档和经验总结

#### 3.3.4 Phase 4: 前后端分离（3-4 周）

**目标**: 实现前后端独立部署

**任务**:
1. 设计 RESTful API
2. 实现 API 层（Controller）
3. 实现 DTO 和转换器
4. 配置 CORS
5. 前端独立部署配置
6. API 文档生成（Swagger/OpenAPI）

**交付物**:
- RESTful API 实现
- API 文档
- 前端独立部署配置
- 部署指南

#### 3.3.5 Phase 5: JWT 认证（2-3 周）

**目标**: 用 JWT 替换 Session 认证

**任务**:
1. 实现 JWT 生成和验证
2. 实现 Token 刷新机制
3. 实现 Token 黑名单
4. 替换 Session 认证
5. 权限系统适配
6. 安全测试

**交付物**:
- JWT 认证实现
- Token 管理服务
- 安全配置文档
- 安全测试报告

#### 3.3.6 Phase 6: 测试和优化（2-3 周）

**目标**: 全面测试和性能优化

**任务**:
1. 功能测试（所有模块）
2. 集成测试
3. 性能测试
4. 安全测试
5. 性能优化
6. 文档完善

**交付物**:
- 测试报告
- 性能对比报告
- 优化建议
- 完整文档

---

## 4. 数据模型

### 4.1 基线指标数据模型

```json
{
  "baseline": {
    "version": "1.0",
    "date": "2026-02-01",
    "system": {
      "moqui_version": "3.1.0-rc2",
      "java_version": "17",
      "gradle_version": "7.x",
      "os": "Windows"
    },
    "components": [
      {
        "name": "moqui-framework",
        "version": "3.1.0-rc2",
        "status": "active"
      }
    ],
    "performance": {
      "startup_time_cold": 15.0,
      "startup_time_hot": 14.0,
      "memory_initial_mb": 200,
      "memory_peak_mb": 500,
      "response_time_home_ms": 500,
      "response_time_login_ms": 300
    },
    "code_stats": {
      "total_files": 1000,
      "java_files": 600,
      "groovy_files": 100,
      "xml_files": 300,
      "java_lines": 50000,
      "groovy_lines": 15000,
      "xml_lines": 20000
    }
  }
}
```

### 4.2 重构进度跟踪模型

```json
{
  "refactoring": {
    "current_phase": "Phase 1",
    "start_date": "2026-02-01",
    "phases": [
      {
        "id": "phase-1",
        "name": "Preparation",
        "status": "in_progress",
        "progress": 50,
        "tasks_completed": 3,
        "tasks_total": 7
      }
    ],
    "milestones": [
      {
        "id": "M1",
        "name": "Baseline Established",
        "date": "2026-02-01",
        "status": "completed"
      }
    ]
  }
}
```

---

## 5. 接口设计

### 5.1 脚本接口

#### 5.1.1 setup-refactor-branch.bat

**输入**: 无（交互式确认）

**输出**:
- Git 标签: `baseline-v1.0`
- Git 分支: `refactor/java-maven-jwt`
- 控制台输出: 操作结果

**错误处理**:
- 检查是否在 Git 仓库中
- 检查是否有未提交的更改
- 检查标签是否已存在
- 检查分支是否已存在

#### 5.1.2 collect-baseline-metrics.bat

**输入**: 无

**输出**:
- JSON 文件: `results/baseline-metrics.json`
- 文本报告: `results/baseline-report.txt`

**收集内容**:
- 系统版本信息
- 性能指标
- 代码统计
- 依赖信息

#### 5.1.3 verify-baseline.bat

**输入**: 无

**输出**:
- 验证报告: `results/baseline-verification.txt`
- 退出码: 0（成功）或 1（失败）

**验证项**:
- 系统能否启动
- 所有组件是否加载
- 关键功能是否正常
- 性能是否符合基线

---

## 6. 安全设计

### 6.1 分支访问控制

**master 分支**:
- 保护级别: 高
- 直接推送: 禁止
- PR 要求: 至少 1 人审查
- 状态检查: CI 必须通过

**refactor 分支**:
- 保护级别: 中（初期）→ 高（后期）
- 直接推送: 允许（初期）→ 禁止（后期）
- PR 要求: 可选（初期）→ 必须（后期）

### 6.2 数据安全

**基线数据**:
- 不包含敏感信息（密码、密钥）
- 配置文件脱敏处理
- 数据库备份加密存储

**代码安全**:
- 定期安全扫描
- 依赖漏洞检查
- 代码审查

---

## 7. 性能设计

### 7.1 性能目标

**构建性能**:
- Maven 构建时间 < Gradle 构建时间 × 1.2
- 增量构建时间 < 30 秒

**运行性能**:
- 启动时间 < 基线 × 1.2
- 内存占用 < 基线 × 1.5
- API 响应时间 < 200ms (P95)

### 7.2 性能监控

**监控指标**:
- 启动时间
- 内存占用
- CPU 使用率
- API 响应时间
- 数据库查询时间

**监控工具**:
- JMX
- Micrometer
- Prometheus + Grafana

---

## 8. 测试设计

### 8.1 测试策略

**单元测试**:
- 覆盖率目标: >70%
- 框架: JUnit 5
- Mock 框架: Mockito

**集成测试**:
- API 测试: RestAssured
- 数据库测试: Testcontainers
- 端到端测试: Selenium

**性能测试**:
- 工具: JMeter
- 场景: 登录、查询、创建、更新
- 并发: 10, 50, 100 用户

### 8.2 测试环境

**开发环境**:
- 本地 H2 数据库
- 嵌入式 Jetty

**测试环境**:
- PostgreSQL 数据库
- 独立 Tomcat

**生产环境**:
- PostgreSQL 集群
- Kubernetes 部署

---

## 9. 部署设计

### 9.1 部署架构

**当前（单体）**:
```
[Browser] → [Moqui WAR] → [H2 Database]
```

**目标（分离）**:
```
[Browser] → [Frontend (Nginx)] → [Backend API (Spring Boot)] → [PostgreSQL]
                                         ↓
                                   [JWT Auth Service]
```

### 9.2 部署流程

**开发环境**:
1. Maven 构建
2. 本地启动（IDE 或命令行）
3. 访问 http://localhost:8080

**测试环境**:
1. Maven 构建
2. Docker 镜像构建
3. Docker Compose 部署
4. 自动化测试

**生产环境**:
1. Maven 构建
2. Docker 镜像构建
3. 推送到镜像仓库
4. Kubernetes 部署
5. 健康检查
6. 流量切换

---

## 10. 监控和日志

### 10.1 监控设计

**系统监控**:
- CPU、内存、磁盘、网络
- JVM 指标（堆、GC、线程）
- 应用指标（请求数、响应时间、错误率）

**业务监控**:
- 用户登录数
- API 调用量
- 业务操作成功率

### 10.2 日志设计

**日志级别**:
- ERROR: 错误信息
- WARN: 警告信息
- INFO: 关键操作
- DEBUG: 调试信息

**日志格式**:
```
[时间] [级别] [线程] [类名] - 消息
```

**日志存储**:
- 本地文件（滚动）
- 集中式日志（ELK）

---

## 11. 文档设计

### 11.1 文档结构

**技术文档**:
- 架构设计文档
- API 文档（Swagger）
- 数据库设计文档
- 部署文档

**用户文档**:
- 用户手册
- 管理员手册
- FAQ

**开发文档**:
- 开发环境搭建
- 编码规范
- 贡献指南

### 11.2 文档维护

**更新频率**:
- 架构文档: 每个 Phase 更新
- API 文档: 自动生成
- 用户文档: 功能变更时更新

**文档审查**:
- 技术审查: 技术负责人
- 用户审查: 产品经理
- 定期审查: 每月一次

---

## 12. 风险管理

### 12.1 技术风险

**风险 1: Groovy 动态特性难以转换**
- 概率: 高
- 影响: 高
- 缓解: 详细分析 Groovy 代码，设计等价的 Java 实现
- 应急: 保留部分 Groovy 代码，逐步迁移

**风险 2: Maven 依赖冲突**
- 概率: 中
- 影响: 中
- 缓解: 使用依赖管理工具，锁定版本
- 应急: 回退到 Gradle

**风险 3: 性能下降**
- 概率: 中
- 影响: 高
- 缓解: 持续性能测试，及时优化
- 应急: 性能调优，必要时回退

### 12.2 项目风险

**风险 1: 时间延期**
- 概率: 中
- 影响: 中
- 缓解: 合理估算，预留缓冲时间
- 应急: 调整范围，延后非关键功能

**风险 2: 人员变动**
- 概率: 低
- 影响: 高
- 缓解: 知识共享，文档完善
- 应急: 快速招聘，知识转移

---

## 13. 成功标准

### 13.1 技术标准

- [ ] 所有 Groovy 代码已转换为 Java
- [ ] Maven 构建成功，所有测试通过
- [ ] 前后端可独立部署
- [ ] JWT 认证正常工作
- [ ] 性能符合目标（启动时间、响应时间、内存占用）
- [ ] 安全测试通过
- [ ] 代码质量符合标准（SonarQube）

### 13.2 业务标准

- [ ] 所有现有功能正常工作
- [ ] 用户体验无明显下降
- [ ] 系统稳定性良好（无严重 bug）
- [ ] 文档完整准确

### 13.3 项目标准

- [ ] 按时完成（5 个月内）
- [ ] 预算控制（人力成本）
- [ ] 团队满意度高

---

## 14. 双向追溯

### 14.1 需求到设计

| 需求 ID | 需求描述 | 设计章节 |
|---------|----------|----------|
| 2.1 | 分支管理 | 3.1 Git 分支管理 |
| 2.2 | 基线文档 | 3.2 基线数据收集 |
| 2.3 | 重构路线图 | 3.3 重构路线图 |

### 14.2 设计到实现

| 设计章节 | 实现任务 |
|----------|----------|
| 3.1 Git 分支管理 | Task 1.1, 1.2 |
| 3.2 基线数据收集 | Task 2.1, 2.2, 2.3 |
| 3.3 重构路线图 | Task 3.1 |

---

**文档版本**: 1.0  
**创建时间**: 2026-02-01  
**作者**: Kiro AI  
**状态**: Approved
