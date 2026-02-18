---
name: project-structure-design
category: other
description: Template for Project Structure Design
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

﻿# Implementation Plan: {{SPEC_NAME}}

## Overview

本实施计划将设计文档中定义的最佳项目结构方案转化为可执行的任务。主要工作包括：
1. 迁移 Moqui 项目到 backend/ 目录并解决目录冲突
2. 创建项目目录结构（frontend/, requirements/, docs/, scripts/, docker/）
3. 生成项目文档和配置文件
4. 生成构建和部署脚本
5. 验证目录结构和构建系统

## Tasks

- [x] 1 创建目录迁移脚本
  - 创建 Python 脚本 `scripts/migrate_structure.py`
  - 实现 DirectoryMigrator 类
  - 实现 Moqui 项目迁移功能
  - 实现目录重命名功能（.kiro → .kiro-moqui, frontend → moqui-frontend）
  - 实现迁移验证功能
  - _Requirements: 1.2, 6.1, 6.2_

- [x]* 1.1 编写目录迁移单元测试
  - 测试目录移动功能
  - 测试目录重命名功能
  - 测试迁移验证功能
  - 测试错误处理（源目录不存在、目标目录已存在等）
  - _Requirements: 1.2, 6.1, 6.2_

- [x] 2 执行 Moqui 项目迁移
  - 运行迁移脚本，将 Moqui 项目从 `.kiro/specs/02-00-source-code-analysis/docs/source-code/moqui-framework/` 移动到 `backend/`
  - 重命名 `backend/.kiro/` 为 `backend/.kiro-moqui/`
  - 重命名 `backend/frontend/` 为 `backend/moqui-frontend/`
  - 验证迁移结果
  - _Requirements: 1.2, 6.1, 6.2_

- [x] 3 创建项目目录结构
  - [x] 3.1 创建 frontend/ 目录结构
    - 创建 `frontend/erp-desktop/` 目录
    - 创建 `frontend/erp-desktop/src/main/` 和 `frontend/erp-desktop/src/renderer/` 目录
    - 创建基础文件结构（package.json, vite.config.js 等）
    - _Requirements: 1.1, 1.3_
  
  - [x] 3.2 创建 requirements/ 目录结构
    - 创建 `requirements/original/` 目录
    - 创建 `requirements/analysis/` 目录
    - 移动客户需求文档到 `requirements/original/`
    - 移动需求分析报告到 `requirements/analysis/`
    - _Requirements: 3.1, 3.2, 3.3, 3.4_
  
  - [x] 3.3 创建 docs/ 目录结构
    - 创建 `docs/architecture/`, `docs/development/`, `docs/deployment/`, `docs/testing/` 目录
    - _Requirements: 4.1_
  
  - [x] 3.4 创建 scripts/ 目录
    - 创建 `scripts/` 目录
    - _Requirements: 5.1_
  
  - [x] 3.5 创建 docker/ 目录结构
    - 创建 `docker/backend/`, `docker/frontend/` 目录
    - _Requirements: 5.4_

- [x]* 3.6 编写目录结构验证测试
  - 测试所有必需目录是否创建
  - 测试目录权限是否正确
  - 测试目录结构是否符合设计
  - _Requirements: 1.1, 3.1, 4.1_

- [x] 4 生成项目文档
  - [x] 4.1 生成项目主 README.md
    - 创建根目录 README.md
    - 包含项目概述、目录结构说明、快速开始指南
    - 包含前后端启动命令
    - _Requirements: 4.2_
  
  - [x] 4.2 生成 backend/README.md
    - 说明 Moqui 后端结构
    - 说明 .kiro-moqui/ 和 moqui-frontend/ 的用途
    - 提供后端开发指南
    - _Requirements: 4.3_
  
  - [x] 4.3 生成 frontend/README.md
    - 说明前端应用结构
    - 提供前端开发指南
    - 说明如何添加新的前端应用
    - _Requirements: 4.3_
  
  - [x] 4.4 生成 MIGRATION_GUIDE.md
    - 说明目录迁移的原因和过程
    - 提供迁移前后的目录对比
    - 说明如何处理 Moqui 项目的更新
    - _Requirements: 6.4_
  
  - [x] 4.5 生成目录命名规范文档
    - 创建 `docs/development/directory-naming-guide.md`
    - 说明目录命名规则和最佳实践
    - _Requirements: 6.3_
  
  - [x] 4.6 生成架构文档
    - 创建 `docs/architecture/system-architecture.md`
    - 创建 `docs/architecture/frontend-architecture.md`
    - 创建 `docs/architecture/backend-architecture.md`
    - _Requirements: 4.4_
  
  - [x] 4.7 生成开发文档
    - 创建 `docs/development/setup-guide.md`
    - 创建 `docs/development/coding-standards.md`
    - 创建 `docs/development/git-workflow.md`
    - _Requirements: 4.4_
  
  - [x] 4.8 生成部署文档
    - 创建 `docs/deployment/deployment-guide.md`
    - 创建 `docs/deployment/docker-guide.md`
    - 创建 `docs/deployment/production-checklist.md`
    - _Requirements: 4.4, 5.5_
  
  - [x] 4.9 生成测试文档
    - 创建 `docs/testing/testing-strategy.md`
    - 创建 `docs/testing/test-data-guide.md`
    - _Requirements: 4.4_
  
  - [x] 4.10 生成配置文件模板和说明
    - 创建配置文件模板（MoquiDevConf.xml, vite.config.js 等）
    - 创建配置说明文档
    - _Requirements: 4.5_

- [x]* 4.11 编写文档完整性测试
  - 测试所有必需文档是否存在
  - 测试文档内容是否包含必要信息
  - _Requirements: 4.2, 4.3, 4.4, 4.5_

- [x] 5 生成构建和部署脚本
  - [x] 5.1 生成后端构建脚本
    - 创建 `scripts/build-backend.sh` 和 `scripts/build-backend.bat`
    - 包含 Gradle 构建命令
    - 包含数据加载命令
    - _Requirements: 5.1, 5.2_
  
  - [x] 5.2 生成前端构建脚本
    - 创建 `scripts/build-frontend.sh` 和 `scripts/build-frontend.bat`
    - 包含 npm 构建命令
    - 支持多个前端应用
    - _Requirements: 5.1, 5.2_
  
  - [x] 5.3 生成联合构建脚本
    - 创建 `scripts/build-all.sh` 和 `scripts/build-all.bat`
    - 依次构建后端和前端
    - 提供构建状态报告
    - _Requirements: 5.1, 5.3_
  
  - [x] 5.4 生成部署脚本
    - 创建 `scripts/deploy.sh` 和 `scripts/deploy.bat`
    - 包含部署前检查
    - 包含部署步骤
    - _Requirements: 5.5_
  
  - [x] 5.5 生成开发环境设置脚本
    - 创建 `scripts/setup-dev.sh` 和 `scripts/setup-dev.bat`
    - 包含依赖安装
    - 包含环境配置
    - _Requirements: 5.1_

- [x]* 5.6 编写构建脚本测试
  - 测试后端构建脚本
  - 测试前端构建脚本
  - 测试联合构建脚本
  - _Requirements: 5.1, 5.2, 5.3_

- [x]* 5.7 编写属性测试：前后端独立构建能力
  - **Property 1: 前后端独立构建能力**
  - 使用 hypothesis 生成测试项目结构
  - 测试后端独立构建
  - 测试前端独立构建
  - 验证构建产物独立
  - **Validates: Requirements 1.4, 5.2, 6.5**

- [x] 6 生成 Docker 配置
  - [x] 6.1 生成后端 Dockerfile
    - 创建 `docker/backend/Dockerfile`
    - 基于 OpenJDK 21
    - 包含 Moqui 运行环境
    - _Requirements: 5.4_
  
  - [x] 6.2 生成前端 Dockerfile
    - 创建 `docker/frontend/Dockerfile`
    - 基于 Node.js 18
    - 包含 Vue.js 构建环境
    - _Requirements: 5.4_
  
  - [x] 6.3 生成 docker-compose.yml
    - 创建 `docker/docker-compose.yml`
    - 配置后端和前端服务
    - 配置网络和卷
    - _Requirements: 5.4_
  
  - [x] 6.4 生成 docker-compose.dev.yml
    - 创建 `docker/docker-compose.dev.yml`
    - 配置开发环境
    - 包含热重载和调试支持
    - _Requirements: 5.4_

- [x]* 6.5 编写 Docker 配置测试
  - 测试 Dockerfile 语法
  - 测试 docker-compose 配置
  - 测试容器构建
  - _Requirements: 5.4_

- [x] 7 生成 Git 配置
  - [x] 7.1 生成 .gitignore
    - 创建根目录 .gitignore
    - 包含 Gradle, Node, IDE, Moqui 相关忽略规则
    - _Requirements: 7.1_
  
  - [x] 7.2 生成 .gitmodules（如果需要）
    - 如果使用 Git 子模块，创建 .gitmodules
    - 配置 Moqui Framework 和组件为子模块
    - _Requirements: 7.3_
  
  - [x] 7.3 更新 Git 配置
    - 配置 Git 属性
    - 配置 Git 钩子（如果需要）
    - _Requirements: 7.3_

- [x]* 7.4 编写 Git 配置测试
  - 测试 .gitignore 规则
  - 测试 .gitmodules 配置（如果有）
  - _Requirements: 7.1, 7.3_

- [x] 8 生成 IDE 配置
  - [x] 8.1 生成 VSCode 配置
    - 创建 `.vscode/settings.json`
    - 创建 `.vscode/extensions.json`
    - 创建 `.vscode/launch.json`
    - _Requirements: 7.2, 7.4_
  
  - [x] 8.2 生成 IntelliJ IDEA 配置建议
    - 创建 `docs/development/intellij-setup.md`
    - 说明 IntelliJ IDEA 配置步骤
    - 提供推荐插件列表
    - _Requirements: 7.2_
  
  - [x] 8.3 生成 IDE 配置指南
    - 创建 `docs/development/ide-setup-guide.md`
    - 说明各种 IDE 的配置方法
    - _Requirements: 7.2_

- [x] 9 验证目录结构
  - [x] 9.1 创建验证脚本
    - 创建 `scripts/validate_structure.py`
    - 实现 StructureValidator 类
    - 实现目录结构验证
    - 实现冲突检测
    - 实现文档完整性检查
    - _Requirements: 6.1, 6.2, 6.5_
  
  - [x] 9.2 运行验证脚本
    - 验证所有必需目录存在
    - 验证无目录冲突
    - 验证文档完整性
    - 生成验证报告
    - _Requirements: 6.1, 6.2, 6.5_

- [x]* 9.3 编写验证脚本测试
  - 测试目录结构验证功能
  - 测试冲突检测功能
  - 测试文档完整性检查功能
  - _Requirements: 6.1, 6.2, 6.5_

- [x] 10 测试构建系统
  - [x] 10.1 测试后端独立构建
    - 在 backend/ 目录运行 `gradle build`
    - 验证构建成功
    - 验证构建产物
    - _Requirements: 1.4, 5.2_
  
  - [x] 10.2 测试前端独立构建
    - 在 frontend/erp-desktop/ 目录运行 `npm run build`
    - 验证构建成功
    - 验证构建产物
    - _Requirements: 1.4, 5.2_
  
  - [x] 10.3 测试联合构建
    - 运行 `scripts/build-all.sh`
    - 验证前后端都构建成功
    - 验证构建顺序正确
    - _Requirements: 5.3_

- [x]* 10.4 编写属性测试：项目可扩展性
  - **Property 2: 项目可扩展性**
  - 使用 hypothesis 生成测试模块
  - 测试添加新前端应用
  - 测试添加新后端服务
  - 测试添加新 Moqui 组件
  - 验证构建系统仍正常工作
  - **Validates: Requirements 8.1, 8.2, 8.3**

- [x] 11 生成扩展指南
  - [x] 11.1 生成前端扩展指南
    - 创建 `docs/development/frontend-extension-guide.md`
    - 说明如何添加新的前端应用
    - 提供前端应用模板
    - _Requirements: 8.1, 8.5_
  
  - [x] 11.2 生成后端扩展指南
    - 创建 `docs/development/backend-extension-guide.md`
    - 说明如何添加新的后端服务
    - _Requirements: 8.2, 8.5_
  
  - [x] 11.3 生成 Moqui 组件扩展指南
    - 创建 `docs/development/moqui-component-guide.md`
    - 说明如何添加新的 Moqui 组件
    - 提供组件模板
    - _Requirements: 8.3, 8.5_

- [x] 12 最终检查和文档更新
  - [x] 12.1 更新 CURRENT_CONTEXT.md
    - 更新当前 Spec 状态为完成
    - 记录项目结构重组的关键决策
    - 为下一个 Spec 做准备
    - _Requirements: All_
  
  - [x] 12.2 生成项目结构总结报告
    - 创建 `.kiro/specs/{{SPEC_NAME}}/reports/structure-summary.md`
    - 总结目录结构变更
    - 列出所有生成的文件和脚本
    - 提供下一步建议
    - _Requirements: All_
  
  - [x] 12.3 最终验证
    - 运行所有验证脚本
    - 确认所有文档完整
    - 确认构建系统正常
    - 生成最终验证报告
    - _Requirements: All_

## Notes

- 任务标记 `*` 的为可选任务，可以跳过以加快 MVP 交付
- 每个任务都引用了具体的需求，确保可追溯性
- 迁移脚本应该提供回滚功能，以防迁移失败
- 所有脚本应该同时提供 Linux/macOS (.sh) 和 Windows (.bat) 版本
- 属性测试使用 Python 的 hypothesis 库，每个测试运行最少 100 次迭代
- 构建测试应该在干净的环境中运行，避免缓存影响结果

---

**任务列表版本**: v1.0  
**创建日期**: {{DATE}}  
**预计完成时间**: 2-3 天（取决于测试覆盖度）



