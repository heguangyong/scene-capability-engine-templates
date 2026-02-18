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

# Design Document: {{SPEC_NAME}}

## Overview

本设计文档定义 331-poc 项目的最佳目录结构，整合 Moqui Framework 后端、Vue.js + Electron 前端、Kiro Spec 体系和客户需求文档。设计目标是创建一个清晰、可维护、可扩展的项目结构，避免目录冲突，支持独立开发和部署。

### 设计原则

1. **前后端分离**: 前后端代码完全独立，可以独立开发、构建和部署
2. **避免冲突**: 解决 Moqui 项目自带的 .kiro/ 和 frontend/ 目录与项目根目录的冲突
3. **清晰组织**: 每个目录职责明确，命名清晰，易于理解
4. **可扩展性**: 支持未来添加新的前端应用、后端服务和 Moqui 组件
5. **工具兼容**: 兼容 Gradle, npm, Git, IDE 等开发工具

### 关键挑战

1. **Moqui 项目的 .kiro/ 目录**: Moqui 项目自带 26 个 Spec，有完整的 .kiro/ 目录
2. **Moqui 项目的 frontend/ 目录**: Moqui 项目包含 3 个前端应用（babelio, moqui-ai-mobile, NovelAnimeDesktop）
3. **客户需求文档**: 需要合理组织 111 项功能需求的文档和分析报告
4. **多仓库管理**: Moqui Framework 和各个组件都是独立的 Git 仓库

## Architecture

### 最佳目录结构方案

```
331-poc/                          # 项目根目录
├── .kiro/                          # 项目级 Kiro Spec 体系
│   ├── specs/                      # 项目 Spec（当前 3 个）
│   │   ├── 01-00-customer-requirements/
│   │   ├── 02-00-source-code-analysis/
│   │   └── {{SPEC_NAME}}/
│   ├── steering/                   # AI 行为规则
│   │   ├── CORE_PRINCIPLES.md
│   │   ├── ENVIRONMENT.md
│   │   ├── CURRENT_CONTEXT.md
│   │   └── RULES_GUIDE.md
│   ├── tools/                      # 开发工具
│   └── README.md
│
├── backend/                        # 后端系统（Moqui Framework）
│   ├── .kiro-moqui/                # Moqui 项目的 Spec 体系（重命名）
│   │   └── specs/                  # Moqui 的 26 个 Spec
│   ├── framework/                  # Moqui Framework 核心
│   ├── runtime/                    # Moqui Runtime
│   │   ├── base-component/
│   │   ├── component/              # Moqui 组件
│   │   │   ├── mantle-udm/
│   │   │   ├── mantle-usl/
│   │   │   ├── SimpleScreens/
│   │   │   ├── HiveMind/
│   │   │   ├── MarbleERP/
│   │   │   └── moqui-ext-erp/      # 自定义 ERP 组件
│   │   ├── conf/
│   │   ├── db/
│   │   └── log/
│   ├── moqui-frontend/             # Moqui 自带的前端应用（重命名）
│   │   ├── babelio/
│   │   ├── moqui-ai-mobile/
│   │   └── NovelAnimeDesktop/
│   ├── build.gradle
│   ├── settings.gradle
│   ├── addons.xml
│   ├── myaddons.xml
│   ├── MoquiInit.properties
│   └── README.md
│
├── frontend/                       # 项目前端应用
│   ├── erp-desktop/                # 主 ERP 桌面应用（Vue.js + Electron）
│   │   ├── src/
│   │   │   ├── main/               # Electron 主进程
│   │   │   └── renderer/           # Vue.js 渲染进程
│   │   ├── package.json
│   │   ├── vite.config.js
│   │   └── README.md
│   └── [future-apps]/              # 未来的前端应用
│
├── requirements/                   # 客户需求文档
│   ├── original/                   # 原始需求文件
│   │   └── customer-requirements.xlsx
│   ├── analysis/                   # 需求分析报告
│   │   ├── requirements-structure.md
│   │   ├── functional-coverage-matrix.md
│   │   └── requirements-summary.md
│   └── README.md
│
├── docs/                           # 项目文档
│   ├── architecture/               # 架构文档
│   │   ├── system-architecture.md
│   │   ├── frontend-architecture.md
│   │   └── backend-architecture.md
│   ├── development/                # 开发文档
│   │   ├── setup-guide.md
│   │   ├── coding-standards.md
│   │   └── git-workflow.md
│   ├── deployment/                 # 部署文档
│   │   ├── deployment-guide.md
│   │   ├── docker-guide.md
│   │   └── production-checklist.md
│   ├── testing/                    # 测试文档
│   │   ├── testing-strategy.md
│   │   └── test-data-guide.md
│   └── README.md
│
├── scripts/                        # 项目级脚本
│   ├── build-all.sh                # 联合构建脚本
│   ├── build-backend.sh            # 后端构建脚本
│   ├── build-frontend.sh           # 前端构建脚本
│   ├── deploy.sh                   # 部署脚本
│   └── setup-dev.sh                # 开发环境设置脚本
│
├── docker/                         # Docker 配置
│   ├── backend/
│   │   └── Dockerfile
│   ├── frontend/
│   │   └── Dockerfile
│   ├── docker-compose.yml
│   └── docker-compose.dev.yml
│
├── .gitignore                      # Git 忽略配置
├── .gitmodules                     # Git 子模块配置（如果需要）
├── README.md                       # 项目主文档
└── MIGRATION_GUIDE.md              # 目录迁移指南

```

### 设计决策

#### 1. Moqui 的 .kiro/ 目录处理

**问题**: Moqui 项目自带 .kiro/ 目录（26 个 Spec），与项目根 .kiro/ 冲突

**方案**: 将 Moqui 的 .kiro/ 重命名为 .kiro-moqui/
- **优点**: 
  - 完全避免冲突
  - 保留 Moqui 项目的 Spec 历史
  - 清晰区分项目级 Spec 和 Moqui 级 Spec
- **缺点**: 
  - 需要更新 Moqui 项目中引用 .kiro/ 的路径（如果有）
- **替代方案**: 
  - 删除 Moqui 的 .kiro/（不推荐，会丢失历史）
  - 将 Moqui 的 Spec 合并到项目 .kiro/（不推荐，混淆职责）

#### 2. Moqui 的 frontend/ 目录处理

**问题**: Moqui 项目包含 frontend/ 目录（3 个前端应用），与项目 frontend/ 冲突

**方案**: 将 Moqui 的 frontend/ 重命名为 moqui-frontend/，放在 backend/ 目录下
- **优点**: 
  - 完全避免冲突
  - 保持 Moqui 前端应用与 Moqui 后端的关联
  - 项目 frontend/ 目录专门用于新开发的前端应用
- **缺点**: 
  - Moqui 的前端应用不在顶层 frontend/ 目录
- **替代方案**: 
  - 将 Moqui 前端应用移到项目 frontend/ 下的子目录（不推荐，混淆职责）
  - 删除 Moqui 前端应用（不推荐，可能需要参考）

#### 3. 后端目录结构

**方案**: 将整个 Moqui 项目放在 backend/ 目录下
- **优点**: 
  - 清晰的前后端分离
  - 后端可以独立构建和部署
  - 避免根目录混乱
- **实施**: 
  - 将下载的 Moqui 项目从 `.kiro/specs/02-00-source-code-analysis/docs/source-code/moqui-framework/` 移动到 `backend/`
  - 重命名 backend/.kiro/ 为 backend/.kiro-moqui/
  - 重命名 backend/frontend/ 为 backend/moqui-frontend/

#### 4. 前端目录结构

**方案**: frontend/ 目录专门用于项目的前端应用
- **主应用**: frontend/erp-desktop/（Vue.js + Electron）
- **未来扩展**: frontend/ 下可以添加更多前端应用
- **独立性**: 每个前端应用有独立的 package.json 和构建配置

#### 5. 客户需求文档组织

**方案**: 创建 requirements/ 目录，分为 original/ 和 analysis/ 子目录
- **original/**: 存放客户提供的原始需求文件（Excel）
- **analysis/**: 存放需求分析报告、功能覆盖度矩阵等
- **优点**: 
  - 清晰区分原始需求和分析结果
  - 便于版本管理和团队协作
  - 与 .kiro/specs/ 分离，避免混淆

#### 6. 项目文档组织

**方案**: 创建 docs/ 目录，按文档类型分类
- **architecture/**: 架构设计文档
- **development/**: 开发指南和规范
- **deployment/**: 部署和运维文档
- **testing/**: 测试策略和指南
- **优点**: 
  - 文档组织清晰
  - 便于查找和维护
  - 支持文档版本管理

## Components and Interfaces

### 1. 目录迁移组件 (DirectoryMigrator)

**职责**: 执行目录重组和文件迁移

**接口**:
```python
class DirectoryMigrator:
    def migrate_moqui_project(source_path: str, target_path: str) -> MigrationResult
    def rename_kiro_directory(backend_path: str) -> bool
    def rename_frontend_directory(backend_path: str) -> bool
    def verify_migration(backend_path: str) -> ValidationResult
```

**功能**:
- 移动 Moqui 项目到 backend/ 目录
- 重命名 .kiro/ 为 .kiro-moqui/
- 重命名 frontend/ 为 moqui-frontend/
- 验证迁移结果

### 2. 目录结构生成器 (DirectoryStructureGenerator)

**职责**: 创建项目目录结构

**接口**:
```python
class DirectoryStructureGenerator:
    def create_frontend_structure() -> bool
    def create_requirements_structure() -> bool
    def create_docs_structure() -> bool
    def create_scripts_structure() -> bool
    def create_docker_structure() -> bool
```

**功能**:
- 创建 frontend/ 目录和子目录
- 创建 requirements/ 目录和子目录
- 创建 docs/ 目录和子目录
- 创建 scripts/ 目录
- 创建 docker/ 目录

### 3. 文档生成器 (DocumentationGenerator)

**职责**: 生成项目文档

**接口**:
```python
class DocumentationGenerator:
    def generate_root_readme() -> str
    def generate_backend_readme() -> str
    def generate_frontend_readme() -> str
    def generate_migration_guide() -> str
    def generate_directory_naming_guide() -> str
```

**功能**:
- 生成项目主 README.md
- 生成 backend/README.md
- 生成 frontend/README.md
- 生成 MIGRATION_GUIDE.md
- 生成目录命名规范文档

### 4. 构建脚本生成器 (BuildScriptGenerator)

**职责**: 生成构建和部署脚本

**接口**:
```python
class BuildScriptGenerator:
    def generate_build_all_script() -> str
    def generate_build_backend_script() -> str
    def generate_build_frontend_script() -> str
    def generate_deploy_script() -> str
    def generate_setup_dev_script() -> str
```

**功能**:
- 生成联合构建脚本
- 生成后端构建脚本
- 生成前端构建脚本
- 生成部署脚本
- 生成开发环境设置脚本

### 5. Docker 配置生成器 (DockerConfigGenerator)

**职责**: 生成 Docker 配置文件

**接口**:
```python
class DockerConfigGenerator:
    def generate_backend_dockerfile() -> str
    def generate_frontend_dockerfile() -> str
    def generate_docker_compose() -> str
    def generate_docker_compose_dev() -> str
```

**功能**:
- 生成后端 Dockerfile
- 生成前端 Dockerfile
- 生成 docker-compose.yml
- 生成 docker-compose.dev.yml

### 6. Git 配置生成器 (GitConfigGenerator)

**职责**: 生成 Git 配置文件

**接口**:
```python
class GitConfigGenerator:
    def generate_gitignore() -> str
    def generate_gitmodules() -> str  # 如果需要
    def update_git_config() -> bool
```

**功能**:
- 生成 .gitignore
- 生成 .gitmodules（如果使用子模块）
- 更新 Git 配置

### 7. IDE 配置生成器 (IDEConfigGenerator)

**职责**: 生成 IDE 配置文件和建议

**接口**:
```python
class IDEConfigGenerator:
    def generate_vscode_config() -> dict
    def generate_intellij_config() -> dict
    def generate_ide_guide() -> str
```

**功能**:
- 生成 VSCode 配置
- 生成 IntelliJ IDEA 配置
- 生成 IDE 配置指南

### 8. 验证器 (StructureValidator)

**职责**: 验证目录结构的正确性

**接口**:
```python
class StructureValidator:
    def validate_directory_structure() -> ValidationResult
    def validate_no_conflicts() -> ValidationResult
    def validate_build_independence() -> ValidationResult
    def validate_documentation() -> ValidationResult
```

**功能**:
- 验证目录结构完整性
- 验证无目录冲突
- 验证前后端构建独立性
- 验证文档完整性

## Data Models

### MigrationResult

```python
@dataclass
class MigrationResult:
    success: bool
    source_path: str
    target_path: str
    files_moved: int
    directories_created: int
    errors: List[str]
    warnings: List[str]
```

### ValidationResult

```python
@dataclass
class ValidationResult:
    valid: bool
    checks_passed: List[str]
    checks_failed: List[str]
    warnings: List[str]
    recommendations: List[str]
```

### DirectoryInfo

```python
@dataclass
class DirectoryInfo:
    path: str
    exists: bool
    is_empty: bool
    file_count: int
    subdirectory_count: int
```

### BuildConfig

```python
@dataclass
class BuildConfig:
    backend_build_command: str
    frontend_build_command: str
    combined_build_command: str
    test_command: str
    deploy_command: str
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: 前后端独立构建能力

*For any* project structure created by this system, running the backend build command in the backend/ directory and the frontend build command in the frontend/ directory should both succeed independently without requiring the other to be built first.

**Validates: Requirements 1.4, 5.2, 6.5**

### Property 2: 项目可扩展性

*For any* new module (frontend application, backend service, or Moqui component) added to the appropriate directory (frontend/, backend/, or backend/runtime/component/), the build system should continue to function correctly and the new module should be properly integrated.

**Validates: Requirements 8.1, 8.2, 8.3**

## Error Handling

### 迁移错误处理

1. **源目录不存在**: 
   - 检查源目录是否存在
   - 提供清晰的错误消息
   - 建议用户检查路径

2. **目标目录已存在**: 
   - 检查目标目录是否为空
   - 询问用户是否覆盖或合并
   - 提供备份选项

3. **文件权限错误**: 
   - 检查文件权限
   - 提供权限修复建议
   - 记录失败的文件

4. **磁盘空间不足**: 
   - 检查可用磁盘空间
   - 提供空间需求估算
   - 建议清理空间

### 验证错误处理

1. **目录结构不完整**: 
   - 列出缺失的目录
   - 提供自动修复选项
   - 记录验证结果

2. **构建失败**: 
   - 捕获构建错误输出
   - 分析常见错误原因
   - 提供修复建议

3. **配置文件错误**: 
   - 验证配置文件语法
   - 检查必需的配置项
   - 提供配置模板

## Testing Strategy

### 双重测试方法

本项目采用单元测试和属性测试相结合的方法：

- **单元测试**: 验证具体的目录创建、文件迁移、文档生成等操作
- **属性测试**: 验证构建独立性和可扩展性等通用属性

### 单元测试

**目录操作测试**:
- 测试目录创建功能
- 测试文件移动功能
- 测试目录重命名功能
- 测试目录验证功能

**文档生成测试**:
- 测试 README.md 生成
- 测试迁移指南生成
- 测试构建脚本生成
- 测试 Docker 配置生成

**边界条件测试**:
- 空目录处理
- 已存在目录处理
- 权限不足处理
- 磁盘空间不足处理

### 属性测试

**属性测试配置**:
- 使用 Python 的 `hypothesis` 库
- 每个属性测试运行最少 100 次迭代
- 每个测试标记引用设计文档中的属性

**Property 1 测试**:
```python
# Feature: {{SPEC_NAME}}, Property 1: 前后端独立构建能力
@given(project_structure())
def test_independent_build_capability(structure):
    # 在 backend/ 目录运行后端构建
    backend_result = run_build(structure.backend_path, "gradle build")
    assert backend_result.success
    
    # 在 frontend/ 目录运行前端构建
    frontend_result = run_build(structure.frontend_path, "npm run build")
    assert frontend_result.success
    
    # 验证构建产物独立
    assert not has_cross_dependencies(backend_result, frontend_result)
```

**Property 2 测试**:
```python
# Feature: {{SPEC_NAME}}, Property 2: 项目可扩展性
@given(project_structure(), new_module())
def test_project_extensibility(structure, module):
    # 添加新模块到适当的目录
    add_module(structure, module)
    
    # 运行构建
    build_result = run_build(structure.root_path, "build-all")
    assert build_result.success
    
    # 验证新模块被正确集成
    assert module_is_integrated(structure, module)
```

### 集成测试

**完整迁移测试**:
1. 准备测试环境（模拟下载的 Moqui 项目）
2. 执行完整的目录迁移流程
3. 验证所有目录和文件都在正确位置
4. 验证构建系统正常工作
5. 验证文档完整性

**构建系统测试**:
1. 测试后端独立构建
2. 测试前端独立构建
3. 测试联合构建
4. 测试 Docker 构建

**工具集成测试**:
1. 测试 Git 配置
2. 测试 IDE 配置
3. 测试构建脚本
4. 测试部署脚本

### 测试数据

**测试项目结构**:
- 创建模拟的 Moqui 项目结构
- 包含 .kiro/ 和 frontend/ 目录
- 包含必要的配置文件

**测试模块**:
- 模拟的前端应用
- 模拟的后端服务
- 模拟的 Moqui 组件

### 测试工具

- **Python**: pytest, hypothesis
- **Shell**: shellcheck（脚本验证）
- **Docker**: docker-compose（容器测试）
- **Git**: git（版本控制测试）

---

**设计版本**: v1.0  
**创建日期**: {{DATE}}  
**设计者**: Kiro AI Assistant
