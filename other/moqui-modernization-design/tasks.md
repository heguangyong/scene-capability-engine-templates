---
name: moqui-modernization-design
category: other
description: Template for Moqui Modernization Design
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

﻿# Moqui 渐进式现代化 - 任务列表

**Spec ID**: {{SPEC_NAME}}  
**版本**: 1.0  
**日期**: 2026-02-02  
**状态**: 执行阶段（KSE 接管中）

---

## 任务概览

本 Spec 的任务分为 4 个主要阶段，共 40 周（10 个月）。

**进度**: 2/15 任务完成 (13%)

---

## Phase 1: Groovy → Java 迁移 (8 周)

### [x] 1. 依赖分析和迁移规划
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 1 周  
**负责人**: TBD

**描述**:
分析 88 个 Groovy 文件的依赖关系，制定详细的迁移计划。

**子任务**:
- [x] 1.1 运行依赖分析脚本
- [x] 1.2 生成依赖图
- [x] 1.3 识别循环依赖
- [x] 1.4 确定迁移顺序（叶子节点优先）
- [x] 1.5 创建迁移检查清单

**验收标准**:
- 完整的依赖关系图
- 清晰的迁移顺序列表
- 识别所有循环依赖
- 迁移计划文档完成

**参考**:
- `.kiro/specs/06-00-groovy-to-java-phase2-p1/scripts/analyze-dependencies.py`
- `.kiro/specs/06-00-groovy-to-java-phase2-p1/patterns/`

---

### [x] 2. 迁移工具和基础设施
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 1 周  
**负责人**: TBD

**描述**:
设置迁移所需的工具和基础设施。

**子任务**:
- [x] 2.1 配置 JaCoCo 代码覆盖率工具
- [x] 2.2 配置 CheckStyle/PMD/SpotBugs
- [x] 2.3 创建转换模式库
- [x] 2.4 设置迁移验证流程
- [x] 2.5 创建迁移仪表板

**验收标准**:
- 所有工具配置完成并可用
- 转换模式库包含常见模式
- 验证流程自动化
- 仪表板实时显示进度

**参考**:
- `.kiro/specs/06-00-groovy-to-java-phase2-p1/scripts/setup-infrastructure.bat`

---

### [x] 3. 批量迁移 Groovy 文件
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 5 周  
**负责人**: AI Agent  
**状态**: 🚧 进行中 - Phase 1 完成，Phase 2 Group 1 完成

**描述**:
按依赖顺序批量迁移 88 个 Groovy 文件到 Java。

**Phase 1: 独立工具类迁移** ✅ 完成
- [x] 3.1.1 JCSCacheToolFactory.java (69 LOC)
- [x] 3.1.2 SimpleSgmlReader.java (159 LOC)
- [x] 3.1.3 JackrabbitRunToolFactory.java (113 LOC)
- [x] 3.1.4 ElFinderConnector.java (302 LOC)
- [x] 3.1.5 EdiHandler.java (479 LOC)
- **总计**: 1,122 LOC

**Phase 2: 核心框架类迁移** 🚧 进行中
- **Group 1: 基础 Facade** ✅ 完成 (490 LOC)
  - [x] 3.2.1 MessageFacadeImpl.java (206 LOC)
  - [x] 3.2.2 LoggerFacadeImpl.java (59 LOC)
  - [x] 3.2.3 CacheFacadeImpl.java (225 LOC)
- **Group 2: 资源和屏幕 Facade** ⏭️ 待开始 (1,638 LOC)
  - [x] 3.2.4 ResourceFacadeImpl.java (524 LOC)
  - [x] 3.2.5 ScreenFacadeImpl.java (417 LOC)
  - [x] 3.2.6 ElasticFacadeImpl.java (697 LOC)
- **Group 3: 事务和实体** ⏭️ 待开始 (3,734 LOC)
  - [x] 3.2.7 TransactionCache.java (390 LOC)
  - [x] 3.2.8 TransactionFacadeImpl.java (605 LOC)
  - [x] 3.2.9 EntityDataLoaderImpl.java (946 LOC)
  - [x] 3.2.10 EntityFacadeImpl.java (1,793 LOC)
- **Group 4: 核心上下文和用户** ⏭️ 待开始 (5,086 LOC)
  - [x] 3.2.11 NotificationMessageImpl.java (418 LOC)
  - [x] 3.2.12 ArtifactExecutionFacadeImpl.java (512 LOC)
  - [x] 3.2.13 UserFacadeImpl.java (890 LOC)
  - [x] 3.2.14 WebFacadeImpl.java (1,083 LOC)
  - [x] 3.2.15 ServiceFacadeImpl.java (754 LOC)
  - [x] 3.2.16 ExecutionContextFactoryImpl.java (1,429 LOC)

**Phase 3: 业务逻辑类迁移** ⏭️ 待开始
- [x] 3.3 迁移剩余 72 个文件（约 20,000 LOC）

**验收标准**:
- 所有 88 个文件迁移完成
- 编译零错误零警告
- 所有测试通过
- 代码覆盖率 ≥ 70%
- 代码质量检查通过

**参考**:
- `design-groovy-migration.md`
- `.kiro/specs/06-00-groovy-to-java-phase2-p1/LAYERED_MIGRATION_STRATEGY.md`

---

### [x] 4. 功能等价验证
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 1 周  
**负责人**: TBD

**描述**:
验证迁移后的 Java 代码功能完全等价于原 Groovy 代码。

**子任务**:
- [x] 4.1 运行完整测试套件
- [x] 4.2 执行端到端测试
- [x] 4.3 性能基准测试
- [x] 4.4 回归测试
- [x] 4.5 生成验证报告

**验收标准**:
- 所有单元测试通过
- 所有集成测试通过
- 性能在基线 20% 以内
- 零功能回归
- 验证报告完整

---

## Phase 2: REST API 层 (12 周)

### [x] 5. REST API 架构设计
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 2 周  
**负责人**: TBD

**描述**:
设计 REST API 层的架构和规范。

**子任务**:
- [x] 5.1 定义 API 端点规范
- [x] 5.2 设计 API 认证机制（JWT）
- [x] 5.3 设计 API 授权机制（RBAC）
- [x] 5.4 定义错误处理规范
- [x] 5.5 创建 OpenAPI 3.0 规范文档

**最新进展（{{DATE}}）**:
- 已提供 FastAPI 风格开发入口：`/docs`、`/redoc`、`/openapi.json`
- 保留兼容入口：`/api/v1/docs`、`/api/v1/docs/openapi.json`、`/api/v1/openapi.json`
- 新增公共健康检查：`/health`、`/api/v1/health`
- 已完成 KSE 接管基线：`kse adopt --verbose`、`kse status`、`kse docs validate --all`
- 已完成 JWT 认证机制设计落地（5.2）：
  - 鉴权中间件：`JwtAuthMiddleware`（Bearer token 校验 + `TOKEN_EXPIRED` / `AUTHENTICATION_REQUIRED`）
  - 认证入口：`/api/v1/auth/login`、`/api/v1/auth/refresh`、`/api/v1/auth/logout`
  - OpenAPI Bearer 方案：`BearerAuth` + 公开认证端点 `security: []`
- 已完成 RBAC 授权机制设计落地（5.3）：
  - 角色模型：`moqui.security.UserGroupMember`
  - 权限映射：`moqui.security.UserGroupPermission`
  - 权限检查视图：`moqui.security.UserPermissionCheck`
  - API 层负责鉴权门禁，服务/实体层继续复用 Moqui 原生 authz 机制
- 已新增认证/授权架构契约测试：
  - `ApiSecurityArchitecturePropertyTest`
  - 覆盖 OpenAPI Bearer 声明、认证端点公开性、RBAC 模型实体存在性

**验收标准**:
- 完整的 API 端点列表
- JWT 认证流程设计
- RBAC 权限模型设计
- 标准化错误响应格式
- OpenAPI 规范文档完成

**参考**:
- `design-rest-api.md`

---

### [x] 6. 实体 CRUD API 实现
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 3 周  
**负责人**: TBD

**描述**:
实现实体的 CRUD REST API。

**子任务**:
- [x] 6.1 实现通用实体 CRUD 控制器
- [x] 6.2 实现实体查询 API（过滤、排序、分页）
- [x] 6.3 实现实体关联查询 API
- [x] 6.4 实现批量操作 API
- [x] 6.5 编写 API 测试

**最新进展（{{DATE}}）**:
- 实体控制器能力增强：
  - 新增实体导航入口：`GET /api/v1/entity-hub`
  - 新增实体目录入口：`GET /api/v1/entities`（支持 `packagePrefix` / `keyword` / 分页）
  - 新增实体定义入口：`GET /api/v1/entities/{entityName}/definition`
  - 新增实体关系入口：`GET /api/v1/entities/{entityName}/relationships`
  - 新增组件分组入口：
    - `GET /api/v1/framework/entities`
    - `GET /api/v1/mantle-usl/entities`
    - `GET /api/v1/mantle-udm/entities`
    - `GET /api/v1/components/{componentName}/entities`
  - 全局实体目录新增 `component` 过滤：`GET /api/v1/entities?component=...`
  - 新增批量入口：`POST /api/v1/entities/{entityName}/batch`
  - 新增关联查询入口：`GET /api/v1/entities/{entityName}/{entityId}/related/{relationshipName}`
- 实体目录组件归属能力增强：
  - `EntityCatalogService` 自动扫描组件 `entity/*.xml` 归属关系（含兜底包名推断）
  - `GET /api/v1/entities/{entityName}/definition` 响应补充 `component`
- 实体查询参数契约增强：
  - `pageIndex/pageSize` 统一归一化（`pageSize` 限制最大 200）
  - `fields`、`orderBy`、filter key 增加字段名白名单校验
  - `orderBy` 支持 `field` / `-field` / `field ASC|DESC` 语法
- 关联查询实现（6.3）：
  - 基于实体关系定义自动解析关系名（`EntityDefinition.getRelationshipInfo()`）
  - 支持 type-one 与 type-many 关系返回
  - type-many 支持过滤、排序、分页、字段投影
- 批量操作实现（6.4）：
  - 支持 `create|update|delete` 混合批处理
  - 支持 `continueOnError` 策略
  - 返回逐条结果与汇总统计（成功/失败/总数）
- 测试补齐（6.5）：
  - 新增 `EntityCatalogContractPropertyTest` 覆盖实体目录相关路由契约
  - 新增 `EntityApiContractPropertyTest` 覆盖实体路由契约、分页归一化、`orderBy/fields/filter` 参数规则
  - 新增 `ScreenCatalogContractPropertyTest` 覆盖 screen 目录路由契约
  - 扩展 `EndpointRegressionPropertyTest` 覆盖 batch/related + entity/screen hub 新路由
  - 扩展 `EntityCatalogContractPropertyTest` 覆盖实体分组入口路由契约
  - `mvn -q -pl app -am test` 通过

**验收标准**:
- 所有实体支持 CRUD 操作
- 查询支持复杂过滤条件
- 支持关联数据加载
- 支持批量创建/更新/删除
- API 测试覆盖率 ≥ 80%

---

### [x] 7. 服务 API 实现
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 4 周  
**负责人**: TBD

**描述**:
将 Moqui Service 暴露为 REST API。

**子任务**:
- [x] 7.1 实现服务调用 API
- [x] 7.2 实现服务参数验证
- [x] 7.3 实现服务事务管理
- [x] 7.4 实现服务异步调用
- [x] 7.5 编写服务 API 测试

**最新进展（{{DATE}}）**:
- 已实现服务目录与调用主入口：
  - `GET /api/v1/services`
  - `GET /api/v1/services/{servicePath}/{serviceVerbNoun}/definition`
  - `POST /api/v1/services/{servicePath}/{serviceVerbNoun}`
- 已实现组件化服务分组入口：
  - `GET /api/v1/service-hub`
  - `GET /api/v1/framework/services`
  - `GET /api/v1/mantle-usl/services`
  - `GET /api/v1/mantle-udm/services`
- 运行态已验证上述分组目录端点返回 200（`framework/mantle-usl/mantle-udm`）
- 服务参数验证主干已落地（已完成）：
  - 在 `ServiceInvocationService` 新增 `validateParameters()` 预校验流程（unknown 参数拦截 + Moqui 参数清洗/校验 + 统一 `VALIDATION_ERROR` 响应）
  - 在 `ServiceController` 中改为“先校验再调用”
  - 新增 `ServiceParameterValidator` 用于错误细节标准化（field/reason/message）
  - 新增 `ServiceParameterValidatorPropertyTest` 覆盖未知参数与 validation detail 结构
  - `mvn test`、`mvn package -DskipTests` 均通过
- 服务事务管理能力已落地（已完成）：
  - 新增 `ServiceTransactionOptions`（`X-Tx-Policy`=`service|use-or-begin|require-new`，`X-Tx-Timeout`）
  - 新增 `ServiceInvocationResult`，在响应中返回 `_tx` 事务元信息（policy/outcome/是否新开事务等）
  - `ServiceInvocationService` 增加 `invokeSyncWithTransaction()`，支持 API 层事务包裹与回滚策略
  - `DocsController` 已为服务调用路径自动暴露 `X-Tx-Policy` 与 `X-Tx-Timeout` 头参数文档
  - 新增 `ServiceTransactionOptionsPropertyTest` 覆盖策略/超时解析与非法值拦截
  - `mvn test`、`mvn package -DskipTests`、`kse docs validate --all` 均通过
- 服务异步调用能力已落地（已完成）：
  - 新增 `ServiceAsyncJobTracker`，提供 `PENDING/RUNNING/SUCCEEDED/FAILED` 生命周期跟踪
  - `ServiceInvocationService.invokeAsync()` 改为内存任务队列 + 后台线程执行
  - `getJobStatus()` 优先返回 in-memory 任务状态，并兼容原数据库 `ServiceJob` 查询
  - 新增 `ServiceAsyncJobTrackerPropertyTest` 覆盖异步任务状态流转
  - `mvn test`、`mvn package -DskipTests` 均通过
- 服务 API 路由与命名契约测试已补齐（7.5）：
  - 新增 `ServiceApiRouteContractPropertyTest`
  - 覆盖服务路由注册完整性、兼容入口可达性、`buildServiceName` path-friendly 转换与非法输入校验
  - 与既有测试组合形成 7.x 覆盖集合：
    - `ServiceParameterValidatorPropertyTest`
    - `ServiceTransactionOptionsPropertyTest`
    - `ServiceAsyncJobTrackerPropertyTest`
    - `ServiceApiDocumentationPropertyTest`
    - `ServiceApiRouteContractPropertyTest`

**验收标准**:
- 所有核心服务可通过 API 调用
- 参数验证完整
- 事务正确管理
- 支持异步调用
- API 测试覆盖率 ≥ 80%

---

### [x] 8. Screen Definition API 实现
**优先级**: ⭐⭐⭐⭐  
**预估时间**: 2 周  
**负责人**: TBD

**描述**:
提供 Screen Definition 的 REST API。

**子任务**:
- [x] 8.1 实现 Screen 定义查询 API
- [x] 8.2 实现 Form 定义查询 API（可从 Screen Definition 中抽取）
- [x] 8.3 实现 Widget 定义查询 API（可从 Screen Definition 中抽取）
- [x] 8.4 实现 Screen 渲染 API（短期方案）
- [x] 8.5 编写 API 测试（Screen 目录路由契约）

**最新进展（{{DATE}}）**:
- Screen 可发现性目录已落地：
  - `GET /api/v1/screen-hub`
  - `GET /api/v1/screens`
  - `GET /api/v1/screens/definition?path=...`
  - 保留兼容：`GET /api/v1/screens/{screenPath}`
- Screen 组件分组目录入口新增：
  - `GET /api/v1/framework/screens`
  - `GET /api/v1/mantle-usl/screens`
  - `GET /api/v1/mantle-udm/screens`
  - `GET /api/v1/components/{componentName}/screens`
  - 全局屏幕目录支持 `component` 过滤：`GET /api/v1/screens?component=...`
- Screen 结构化查询能力增强：
  - 新增 `GET /api/v1/screens/forms?path=...`（提取该 Screen 的表单定义）
  - 新增 `GET /api/v1/screens/widgets?path=...`（提取该 Screen 的 widgets 结构）
  - `ScreenService` 新增 `getScreenForms()` 与 `getScreenWidgets()`，复用 `getScreenDefinition()` 结果做结构提取
- Screen 渲染短期方案落地：
  - 新增 `GET /api/v1/screens/render?path=...`
  - `ScreenService.renderScreenHtml()` 返回 preview HTML（含 form/transition 摘要），便于前端快速联调
  - 响应数据包含：`renderMode=preview-html`、`contentType=text/html; charset=utf-8`、`html`
- 测试更新：
  - 扩展 `ScreenCatalogContractPropertyTest` 覆盖 screen 分组路由契约
  - 扩展 `ScreenCatalogContractPropertyTest` 覆盖 forms/widgets 新路由契约
  - 扩展 `EndpointRegressionPropertyTest` 覆盖新增 screen/ entity 分组入口
  - `mvn -q -pl app -am "-Dtest=ScreenCatalogContractPropertyTest,ServiceApiDocumentationPropertyTest,EndpointRegressionPropertyTest" test` 通过
  - `mvn -q -pl app -am test` 通过

**验收标准**:
- 可查询所有 Screen 定义
- 返回 JSON 格式的组件树
- 渲染 API 返回 HTML（短期）
- API 响应时间 < 200ms
- API 测试覆盖率 ≥ 80%

---

### [x] 9. API 文档和测试工具
**优先级**: ⭐⭐⭐⭐  
**预估时间**: 1 周  
**负责人**: TBD

**描述**:
完善 API 文档和测试工具。

**子任务**:
- [x] 9.1 生成 Swagger UI 文档
- [x] 9.2 创建 Postman Collection
- [x] 9.3 编写 API 使用指南
- [x] 9.4 创建 API 示例代码（OpenAPI 目录参数样例）
- [x] 9.5 设置 API 监控

**最新进展（{{DATE}}）**:
- FastAPI 风格文档入口已稳定：`/docs`、`/redoc`、`/openapi.json`
- OpenAPI 参数说明增强：
  - 服务目录端点增加 `component/q/includeParams/pageIndex/pageSize`
  - 实体目录端点增加 `component/packagePrefix/keyword/pageIndex/pageSize`
  - 屏幕目录端点增加 `component/keyword/pageIndex/pageSize`
  - `GET /api/v1/screens/definition` 增加必填 `path` 参数说明
  - `GET /api/v1/screens/forms` 与 `GET /api/v1/screens/widgets` 增加必填 `path` 参数说明
- 新增测试 `ServiceApiDocumentationPropertyTest`：
  - 校验服务事务头参数（既有）
  - 校验目录型 API query 参数已进入 OpenAPI（新增）
- 新增交付物（9.2/9.3）：
  - `docs/api/331-poc-rest-api.postman_collection.json`
  - `docs/api/REST_API_USAGE_GUIDE.md`
  - `README.md` 增加 API 指南与 Postman 链接
- API 监控最小实现已落地（9.5）：
  - 新增监控端点：
    - `GET /metrics`（Prometheus text）
    - `GET /api/v1/metrics`（JSON 指标快照）
    - `GET /ready`、`GET /api/v1/ready`（readiness）
  - 新增 `ApiMetricsService`（in-memory 指标聚合）
  - 新增 `MetricsMiddleware`（请求起始采样）
  - `RestServer` 在请求收尾统一记录：method/route/status/duration/in-flight
  - 新增测试 `MonitoringApiContractPropertyTest` 并扩展回归与文档契约测试
- 本次验证：`mvn -q -pl app -am test` 通过

**验收标准**:
- Swagger UI 可访问且完整
- Postman Collection 包含所有 API
- API 使用指南清晰易懂
- 示例代码可运行
- API 监控仪表板可用

---

## Phase 3: 前后端分离 (8 周)

### [x] 10. Vue3 前端项目初始化
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 1 周  
**负责人**: TBD

**描述**:
创建 Vue3 前端项目并配置开发环境。

**子任务**:
- [x] 10.1 创建 Vue3 + TypeScript 项目
- [x] 10.2 配置 Vue Router
- [x] 10.3 配置 Pinia 状态管理
- [x] 10.4 配置 Axios HTTP 客户端
- [x] 10.5 配置开发环境（HMR, DevTools）

**验收标准**:
- 项目结构清晰
- 开发服务器可启动
- 热模块替换工作正常
- TypeScript 配置正确
- 开发工具可用

---

### [x] 11. API 客户端实现
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 2 周  
**负责人**: TBD

**描述**:
实现前端 API 客户端，封装所有后端 API 调用。

**子任务**:
- [x] 11.1 实现 API 基础客户端（Axios 封装）
- [x] 11.2 实现实体 API 客户端
- [x] 11.3 实现服务 API 客户端
- [x] 11.4 实现 Screen API 客户端
- [x] 11.5 实现认证拦截器

**验收标准**:
- 所有 API 调用封装完成
- 支持请求/响应拦截
- 支持错误处理
- 支持 JWT 认证
- TypeScript 类型定义完整

---

### [x] 12. Moqui 渲染引擎集成（短期方案）
**优先级**: ⭐⭐⭐⭐⭐  
**预估时间**: 3 周  
**负责人**: TBD

**描述**:
集成 Moqui 渲染引擎到 Vue3 前端（使用后端渲染 API）。

**子任务**:
- [x] 12.1 实现 Screen 组件（调用后端渲染 API）
- [x] 12.2 实现 Form 组件
- [x] 12.3 实现 Widget 组件
- [x] 12.4 实现事件处理
- [x] 12.5 实现数据绑定

**验收标准**:
- 所有 Moqui 组件可在 Vue 中使用
- 渲染结果与后端一致
- 事件处理正常
- 数据绑定正常
- 性能可接受

**参考**:
- `design-rendering-engine.md`

---

### [x] 13. 核心页面迁移
**优先级**: ⭐⭐⭐⭐  
**预估时间**: 2 周  
**负责人**: TBD

**描述**:
迁移核心页面到 Vue3 前端。

**子任务**:
- [x] 13.1 迁移登录页面
- [x] 13.2 迁移主页面
- [x] 13.3 迁移实体列表页面
- [x] 13.4 迁移实体详情页面
- [x] 13.5 迁移表单页面

**验收标准**:
- 所有核心页面迁移完成
- 功能完全等价
- UI/UX 保持一致
- 响应式设计
- 无障碍性支持

---

## Phase 4: 渲染引擎适配 (12 周)

### [x] 14. 渲染引擎 JavaScript 移植
**优先级**: ⭐⭐⭐⭐  
**预估时间**: 8 周  
**负责人**: TBD

**描述**:
将 Moqui 渲染引擎移植到 JavaScript，实现完全前端化。

**子任务**:
- [x] 14.1 移植 FreeMarker 引擎到 JavaScript
- [x] 14.2 移植 Screen Macros 到 JavaScript
- [x] 14.3 实现 Screen Definition 解析器
- [x] 14.4 实现组件渲染器
- [x] 14.5 性能优化

**验收标准**:
- 渲染引擎可在浏览器运行
- 支持所有 Moqui 组件类型
- 渲染结果与后端一致
- 性能优于后端渲染
- 首屏渲染时间 < 1s

**参考**:
- `design-rendering-engine.md`
- `design-vue-integration.md`

**风险**:
- 技术可行性需要原型验证
- 如果不可行，保留短期方案（后端渲染 API）

---

### [x] 15. Electron 桌面应用支持
**优先级**: ⭐⭐⭐  
**预估时间**: 4 周  
**负责人**: TBD

**描述**:
支持将应用打包为 Electron 桌面应用。

**子任务**:
- [x] 15.1 配置 Electron 项目
- [x] 15.2 实现主进程和渲染进程通信
- [x] 15.3 实现本地数据存储
- [x] 15.4 实现系统托盘
- [x] 15.5 实现自动更新

**验收标准**:
- 可打包为 Windows/Mac/Linux 应用
- 支持离线运行
- 本地数据存储工作正常
- 系统托盘功能正常
- 自动更新功能正常
- 安装包大小 < 100MB
- 启动时间 < 3s

**参考**:
- 现有 Electron 项目: `frontend/`

---

## 正确性属性测试

### 属性 1: Groovy-Java 功能等价性
**测试方法**: 对比测试  
**验证**: Task 4

### 属性 2: API 响应正确性
**测试方法**: 集成测试  
**验证**: Task 6, 7, 8

### 属性 3: 前后端数据一致性
**测试方法**: 端到端测试  
**验证**: Task 13

### 属性 4: 渲染结果一致性
**测试方法**: 视觉回归测试  
**验证**: Task 12, 14

### 属性 5: 性能保持
**测试方法**: 性能基准测试  
**验证**: Task 4, 9, 14

---

## 里程碑

### M1: Groovy 迁移完成 (Week 8)
- Task 1-4 完成
- 所有 Groovy 代码迁移到 Java
- 功能等价验证通过

### M2: REST API 层完成 (Week 20)
- Task 5-9 完成
- 所有核心 API 实现
- API 文档完整

### M3: 前后端分离完成 (Week 28)
- Task 10-13 完成
- Vue3 前端可独立运行
- 核心页面迁移完成

### M4: 渲染引擎适配完成 (Week 40)
- Task 14-15 完成
- 完全前端化渲染
- Electron 桌面应用支持

---

## 风险和依赖

### 关键依赖
- Task 2 依赖 Task 1
- Task 3 依赖 Task 2
- Task 6-8 依赖 Task 5
- Task 11-13 依赖 Task 6-8
- Task 14 依赖 Task 12

### 关键风险
- **Risk 1**: 渲染引擎移植技术可行性（Task 14）
  - 缓解: 提前进行原型验证
  - 备选: 保留短期方案（Task 12）

- **Risk 2**: 性能降级（Task 4, 9, 14）
  - 缓解: 每个阶段进行性能测试
  - 备选: 性能优化专项

- **Risk 3**: 兼容性问题（Task 4, 13）
  - 缓解: 充分的回归测试
  - 备选: 新旧系统并行运行

---

## 资源需求

### 人力
- 后端开发: 2 人
- 前端开发: 2 人
- 测试工程师: 1 人
- 架构师: 1 人（兼职）

### 时间
- 总计: 40 周（10 个月）
- 每周工作: 40 小时/人

### 工具
- 开发环境: IntelliJ IDEA, VS Code
- 测试工具: JUnit, Vitest, Postman
- 监控工具: Grafana, Prometheus
- 文档工具: Swagger UI, Markdown

---

## 下一步

1. **审查任务列表**: 确认任务完整性和可行性
2. **分配任务**: 确定每个任务的负责人
3. **创建项目计划**: 使用项目管理工具（Jira, Trello）
4. **启动 Task 1**: 开始依赖分析和迁移规划

---

**版本**: 1.0  
**状态**: 待审查  
**下一步**: 任务分配和项目启动

