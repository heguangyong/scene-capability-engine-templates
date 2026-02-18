---
name: architecture-restructuring
category: other
description: Template for Architecture Restructuring
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Implementation Plan: 架构重构 (10-00)

## Overview

将 331-poc 从 Moqui 遗留架构重构为标准纯 Java REST API 项目。采用渐进式迁移策略：先归档遗留组件 → 创建 Maven 构建 → 实现轻量级启动 → 重组项目结构 → 清理 Gradle → 验证回归。

## Tasks

### [x] 1. 归档 Screen Engine 和遗留资源
### [x] 2. 创建 NoOpScreenFacade 与 ECFI 适配
### [x] 3. Checkpoint：Groovy 移除后构建验证
### [x] 4. 实现 LightweightBootstrap
### [x] 5. Checkpoint：轻量启动验证
### [x] 6. 创建 Maven 构建系统
### [x] 7. 重组项目结构
### [x] 8. Checkpoint：Maven 构建验证
### [x] 9. 清理遗留组件
### [x] 10. 移除 Gradle 构建文件
### [x] 11. 最终验证和回归测试
### [x] 12. Final checkpoint：迁移完成确认

- [x] 1 归档 Screen Engine 和遗留前端资源
  - [x] 1.1 创建 `docs/legacy-screen-engine/` 归档目录结构
    - 创建目录 `docs/legacy-screen-engine/groovy/`, `docs/legacy-screen-engine/screen/`, `docs/legacy-screen-engine/template/`
    - 创建 `docs/legacy-screen-engine/README.md` 说明归档目的和内容索引
    - _Requirements: 2.2, 2.6_
  - [x] 1.2 归档 Groovy 源文件和 Screen Engine Java 类
    - 移动 `ScreenForm.groovy` 和 `ScreenRenderImpl.groovy` 到归档目录
    - 复制 `ScreenFacadeImpl.java` 到归档目录（原文件后续由 NoOp 替换）
    - _Requirements: 2.2, 2.6_
  - [x] 1.3 归档 Screen XML 定义和 FreeMarker 模板
    - 复制 `framework/screen/` 目录到归档
    - 复制 `framework/template/` 目录到归档
    - 复制各组件的 `screen/` 目录（SimpleScreens、webroot 等）到归档
    - _Requirements: 2.6_

- [x] 2 创建 NoOpScreenFacade 和修改 ECFI
  - [x] 2.1 实现 NoOpScreenFacade
    - 在 `framework/src/main/java/org/moqui/impl/screen/` 创建 `NoOpScreenFacade.java`
    - 实现 ScreenFacade 接口，所有方法抛出 UnsupportedOperationException 或返回空值
    - _Requirements: 2.1_
  - [x] 2.2 修改 ExecutionContextFactoryImpl 支持 REST-only 模式
    - 添加 `moqui.rest.only` 系统属性检查
    - 当 `moqui.rest.only=true` 时，使用 NoOpScreenFacade 替代 ScreenFacadeImpl
    - 跳过 ScreenFacadeImpl 的 warmCache 调用
    - _Requirements: 2.1, 5.2_
  - [x] 2.3 修改 ExecutionContextImpl 兼容 NoOpScreenFacade
    - 移除 screenFacade 的 null 检查异常（改为允许 NoOp 实例）
    - _Requirements: 2.1_
  - [x] 2.4 从 framework 编译源集中移除 Groovy 文件
    - 删除 `src/main/groovy/org/moqui/impl/screen/ScreenForm.groovy`
    - 删除 `src/main/groovy/org/moqui/impl/screen/ScreenRenderImpl.groovy`
    - 修改 `framework/build.gradle` 移除 joint compilation 配置（如果 groovy 目录为空则移除 groovy 插件）
    - _Requirements: 2.2, 2.3_
  - [x]* 2.5 编写 NoOpScreenFacade 单元测试
    - 验证所有方法抛出 UnsupportedOperationException
    - 验证 ECFI 在 REST-only 模式下使用 NoOpScreenFacade
    - _Requirements: 2.1_

- [x] 3 Checkpoint - 验证 Groovy 移除后构建通过
  - 运行 `gradlew clean build`，确保零错误
  - 确认不再有 `compileGroovy` 任务或 Groovy 源文件参与编译
  - Ensure all tests pass, ask the user if questions arise.

- [x] 4 实现 LightweightBootstrap
  - [x] 4.1 创建 LightweightBootstrap 类
    - 在 `rest-api/src/main/java/org/moqui/rest/` 创建 `LightweightBootstrap.java`
    - 实现 `initialize(RestConfig)` 方法：设置系统属性、创建 ECFI、返回 ECF
    - 实现 `shutdown(ECF)` 方法
    - 设置 `moqui.rest.only=true` 系统属性
    - _Requirements: 5.1, 5.2, 5.3, 5.4_
  - [x] 4.2 增强 RestConfig 支持 application.properties
    - 扩展 RestConfig 支持从 classpath 根目录加载 `application.properties`
    - 添加 `moqui.runtime`、`moqui.conf` 配置项
    - 添加数据库连接配置项（db.url, db.username, db.password, db.pool.size）
    - 保持环境变量覆盖机制
    - _Requirements: 7.1, 7.2, 7.3, 7.5_
  - [x] 4.3 修改 RestApplication 使用 LightweightBootstrap
    - 替换当前的 `Moqui.getExecutionContextFactory()` / `Moqui.dynamicInit()` 调用
    - 使用 LightweightBootstrap.initialize() 初始化
    - 添加启动时间日志
    - _Requirements: 5.1, 5.3, 5.5_
  - [x]* 4.4 编写 LightweightBootstrap 属性测试
    - **Property 3: Bootstrap facade initialization**
    - **Validates: Requirements 5.1**
  - [x]* 4.5 编写 RestConfig 增强属性测试
    - **Property 1: Configuration resolution priority**
    - **Validates: Requirements 5.5, 7.1, 7.2, 7.3**

- [x] 5 Checkpoint - 验证轻量级启动工作正常
  - 运行 `gradlew clean build`
  - 验证 REST API 可通过 LightweightBootstrap 启动
  - Ensure all tests pass, ask the user if questions arise.
  - ✅ {{DATE}} 已执行：`gradlew :runtime:component:rest-api:clean :runtime:component:rest-api:compileJava`、`gradlew :runtime:component:rest-api:test`、`gradlew clean build`（均成功）
  - ✅ 2026-02-08 已执行：`java -jar backend/app/target/app.jar` 启动成功，`GET /api/v1/docs` 返回 200

- [x] 6 创建 Maven 构建系统
  - [x] 6.1 创建父 POM
    - 在 `backend/` 创建 `pom.xml`，配置多模块项目（framework, app）
    - 配置 Java 21、UTF-8 编码、依赖管理
    - _Requirements: 4.1, 4.2_
  - [x] 6.2 创建 framework 模块 POM
    - 在 `backend/framework/` 创建 `pom.xml`
    - 迁移所有 framework 依赖（保持版本号一致）
    - 配置 `src/main/java` 为唯一源目录（不再有 groovy 源）
    - 配置资源目录包含 entity/、service/、screen/、data/、template/ 等
    - _Requirements: 4.1, 4.3_
  - [x] 6.3 创建 app 模块 POM
    - 在 `backend/app/` 创建 `pom.xml`（对应当前 rest-api 组件）
    - 依赖 framework 模块
    - 配置 Maven Shade Plugin 产出可执行 fat JAR
    - 配置 manifest Main-Class 为 RestApplication
    - 迁移 rest-api 的 Jetty 和 jqwik 依赖
    - _Requirements: 3.1, 3.2, 4.1, 4.7_
  - [x]* 6.4 编写依赖版本一致性属性测试
    - **Property 2: Dependency version preservation**
    - **Validates: Requirements 4.3**

- [x] 7 重组项目结构
  - [x] 7.1 移动 REST API 源码到 app 模块
    - 将 `runtime/component/rest-api/src/` 移动到 `app/src/`
    - 更新包路径和 import（如有需要）
    - _Requirements: 6.1, 6.2_
  - [x] 7.2 整理 runtime 目录
    - 保留 `runtime/conf/`、`runtime/db/`、`runtime/log/`
    - 保留 `runtime/component/mantle-udm/`（实体定义）
    - 保留 `runtime/component/mantle-usl/`（服务定义）
    - 标记待移除组件：webroot、SimpleScreens、PopCommerce、MarbleERP、HiveMind、tools、moqui-fop
    - _Requirements: 6.3, 6.4, 8.1, 8.2, 8.3_
  - [x] 7.3 创建 application.properties
    - 在 `app/src/main/resources/` 创建 `application.properties`
    - 配置默认值：server、database、moqui runtime/conf 路径
    - _Requirements: 7.1, 7.3_

- [x] 8 Checkpoint - 验证 Maven 构建
  - 运行 `mvn clean compile` 确保零错误
  - 运行 `mvn test` 确保所有测试通过
  - 运行 `mvn package` 确保产出可执行 JAR
  - Ensure all tests pass, ask the user if questions arise.
  - ✅ 2026-02-08 已执行：`mvn clean compile`、`mvn test`、`mvn package -DskipTests`

- [x] 9 清理遗留组件
  - [x] 9.1 移除不需要的 runtime 组件
    - 移除 `runtime/component/webroot/`
    - 移除 `runtime/component/SimpleScreens/`
    - 移除 `runtime/component/PopCommerce/`
    - 移除 `runtime/component/MarbleERP/`
    - 移除 `runtime/component/HiveMind/`
    - 移除 `runtime/component/tools/`
    - 移除 `runtime/component/moqui-fop/`
    - _Requirements: 8.1, 8.4_
  - [x] 9.2 移除 MoquiStart 和 WAR 相关文件
    - 删除 `framework/src/start/` 目录（MoquiStart.java）
    - 删除 `framework/src/execWar/` 目录
    - 删除 `framework/src/main/webapp/WEB-INF/` 目录
    - _Requirements: 3.4, 3.5, 3.6_
  - [x] 9.3 移除 component.xml 自动发现机制
    - 移除或修改 ECFI 中的 `initComponents()` 方法，使用显式模块声明
    - _Requirements: 8.5_

- [x] 10 移除 Gradle 构建文件
  - [x] 10.1 删除 Gradle 文件
    - 删除 `backend/build.gradle`
    - 删除 `backend/settings.gradle`
    - 删除 `backend/framework/build.gradle`
    - 删除 `backend/runtime/component/rest-api/build.gradle`
    - 删除 `backend/gradlew`、`backend/gradlew.bat`
    - 删除 `backend/gradle/` 目录
    - _Requirements: 4.6_

- [x] 11 最终验证和回归测试
  - [x] 11.1 构建验证
    - 运行 `mvn clean package` 确保零错误零警告
    - 验证 JAR 文件大小小于原 WAR 文件
    - _Requirements: 9.1, 9.6_
  - [x] 11.2 启动验证
    - 运行 `java -jar target/app.jar` 验证服务器启动
    - 验证 Entity Engine 初始化成功
    - 验证 Swagger UI 在 `/api/v1/docs` 可访问
    - _Requirements: 3.3, 9.3, 9.4_
  - [x]* 11.3 端点回归属性测试
    - **Property 4: Endpoint regression**
    - **Validates: Requirements 2.5, 9.5**
  - [x] 11.4 运行全量测试套件
    - 运行 `mvn test` 确保所有单元测试和属性测试通过
    - _Requirements: 9.2_

- [x] 12 Final checkpoint - 确认迁移完成
  - 确认所有测试通过
  - 确认 `java -jar` 启动正常
  - 确认无 Gradle 文件残留
  - Ensure all tests pass, ask the user if questions arise.
  - ✅ 2026-02-08 验证：`mvn clean compile`、`mvn test`、`mvn package -DskipTests` 均成功；`GET /api/v1/docs` 返回 200；Gradle 关键文件已删除

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- 迁移采用渐进式策略，每个 Checkpoint 确保系统可用
- Gradle 文件在 Maven 完全验证通过后才删除（Task 10）
- Screen Engine 相关文件归档到 `docs/legacy-screen-engine/` 而非删除，供前端开发参考
- Property tests 使用 jqwik 1.9.0，最少 100 次迭代
