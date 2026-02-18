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

# Moqui 渐进式现代化 - 架构设计

**Spec ID**: {{SPEC_NAME}}  
**版本**: 1.0  
**日期**: 2026-02-02

---

## 设计概述

本设计采用**渐进式现代化**策略，保留 Moqui 框架的优秀部分，只替换有问题的部分。

### 核心原则

1. **保留优秀资产** - Moqui 框架、组件系统、业务逻辑
2. **替换问题部分** - Groovy、XML Service、后端渲染
3. **渐进式迁移** - 分阶段实施，降低风险
4. **功能等价** - 迁移后功能完全一致
5. **性能保持** - 性能不降级

---

## 架构层次

### 整体架构

```
┌─────────────────────────────────────────────────────────┐
│                    Frontend Layer                        │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Vue3 Application + Electron (Optional)            │ │
│  │  ┌──────────────────────────────────────────────┐ │ │
│  │  │  Moqui Rendering Engine (JavaScript Port)   │ │ │
│  │  │  - Screen Component Renderer                 │ │ │
│  │  │  - Form/Widget/Section Renderer              │ │ │
│  │  │  - FreeMarker Template Engine (JS)           │ │ │
│  │  └──────────────────────────────────────────────┘ │ │
│  │  ┌──────────────────────────────────────────────┐ │ │
│  │  │  Vue3 Components                             │ │ │
│  │  │  - Router, State Management, UI Components   │ │ │
│  │  └──────────────────────────────────────────────┘ │ │
│  └────────────────────────────────────────────────────┘ │
└───────────────────────┬─────────────────────────────────┘
                        │ REST API (JSON)
                        │ JWT Authentication
┌───────────────────────▼─────────────────────────────────┐
│                    REST API Layer (New)                  │
│  ┌────────────────────────────────────────────────────┐ │
│  │  JAX-RS / Moqui REST Support                       │ │
│  │  ┌──────────────────────────────────────────────┐ │ │
│  │  │  REST Controllers (Pure Java)                │ │ │
│  │  │  - Entity CRUD APIs                          │ │ │
│  │  │  - Service APIs                              │ │ │
│  │  │  - Screen Definition APIs                    │ │ │
│  │  └──────────────────────────────────────────────┘ │ │
│  │  ┌──────────────────────────────────────────────┐ │ │
│  │  │  Swagger/OpenAPI Documentation               │ │ │
│  │  └──────────────────────────────────────────────┘ │ │
│  └────────────────────────────────────────────────────┘ │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│              Moqui Framework (Preserved)                 │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Service Engine (Pure Java - Migrated from Groovy)│ │
│  │  - Service Facade                                  │ │
│  │  - Service Runner                                  │ │
│  │  - Service Callbacks                               │ │
│  └────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Entity Engine                                     │ │
│  │  - Entity Facade                                   │ │
│  │  - Entity Find                                     │ │
│  │  - Entity Value                                    │ │
│  └────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Screen Engine (Definition Only)                   │ │
│  │  - Screen Facade                                   │ │
│  │  - Screen Definition                               │ │
│  │  - Component Definitions (XML)                     │ │
│  └────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────┐ │
│  │  Core Services                                     │ │
│  │  - Transaction Management                          │ │
│  │  - Security Framework                              │ │
│  │  - Cache System (Hazelcast)                        │ │
│  │  - L10n, Resource, Logger                          │ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## 关键设计决策

### 1. 保留 Moqui 框架

**决策**: 不使用 Spring Boot 或其他框架替换 Moqui

**理由**:
- Moqui 框架设计优秀，经过多年验证
- 实体引擎功能强大，支持多种数据库
- 服务引擎灵活，支持事务管理
- 缓存系统（Hazelcast）成熟稳定
- 无需重新发明轮子

**影响**:
- 保持系统稳定性
- 降低迁移风险
- 保留已有投资
- 开发团队无需学习新框架

---

### 2. REST API 层设计

**决策**: 在 Moqui 之上添加 REST API 层

**技术选型**:
- **选项 A**: JAX-RS (Jersey/RESTEasy)
  - 优点: Java 标准，成熟稳定
  - 缺点: 需要额外依赖

- **选项 B**: Moqui 内置 REST 支持
  - 优点: 无额外依赖，与 Moqui 集成好
  - 缺点: 功能相对简单

- **推荐**: 使用 Moqui 内置 REST 支持 + 自定义扩展

**实现方式**:
```java
// REST Controller 示例
@Path("/api/v1/entities/{entityName}")
public class EntityRestController {
    
    @Inject
    private ExecutionContextFactory ecf;
    
    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Response list(@PathParam("entityName") String entityName,
                        @QueryParam("filter") String filter) {
        ExecutionContext ec = ecf.getExecutionContext();
        EntityList list = ec.entity.find(entityName)
            .condition(filter)
            .list();
        return Response.ok(list).build();
    }
    
    @POST
    @Consumes(MediaType.APPLICATION_JSON)
    @Produces(MediaType.APPLICATION_JSON)
    public Response create(@PathParam("entityName") String entityName,
                          Map<String, Object> data) {
        ExecutionContext ec = ecf.getExecutionContext();
        EntityValue value = ec.entity.makeValue(entityName);
        value.setAll(data);
        value.create();
        return Response.ok(value).build();
    }
}
```

---

### 3. Groovy 到 Java 迁移策略

**决策**: 手动迁移 + 自动化验证

**迁移流程**:
1. 分析 Groovy 文件依赖关系
2. 按依赖顺序迁移（叶子节点优先）
3. 每个文件迁移后立即测试
4. 使用转换模式库加速迁移

**转换模式**:
```groovy
// Groovy 代码
def result = ec.entity.find("Order")
    .condition("customerId", customerId)
    .list()

// Java 代码
EntityList result = ec.entity().find("Order")
    .condition("customerId", customerId)
    .list();
```

详见: [design-groovy-migration.md](design-groovy-migration.md)

---

### 4. 渲染引擎迁移方案

**决策**: 采用混合方案（短期）+ JavaScript 移植（长期）

**短期方案（Phase 3）**:
- 后端提供渲染 API
- 前端调用 API 获取渲染结果
- 快速实现前后端分离

**长期方案（Phase 4）**:
- 将 FreeMarker 引擎移植到 JavaScript
- 将 Screen Macros 移植到 JavaScript
- 完全前端化渲染

**技术可行性**:
- FreeMarker 有 JavaScript 实现（freemarker.js）
- Screen Macros 可转换为 Vue 组件
- 需要原型验证

详见: [design-rendering-engine.md](design-rendering-engine.md)

---

### 5. Vue3 集成设计

**决策**: Moqui 组件 → Vue 组件映射

**映射策略**:
```xml
<!-- Moqui Screen Definition -->
<screen>
    <widgets>
        <form name="OrderForm">
            <field name="customerId">
                <default-field><text-line/></default-field>
            </field>
            <field name="orderDate">
                <default-field><date-time/></default-field>
            </field>
        </form>
    </widgets>
</screen>
```

```vue
<!-- Vue Component -->
<template>
  <MoquiForm name="OrderForm" :definition="formDef">
    <MoquiField name="customerId" type="text-line" />
    <MoquiField name="orderDate" type="date-time" />
  </MoquiForm>
</template>

<script setup>
import { MoquiForm, MoquiField } from '@/components/moqui'
import { useFormDefinition } from '@/composables/moqui'

const formDef = useFormDefinition('OrderForm')
</script>
```

详见: [design-vue-integration.md](design-vue-integration.md)

---

## 数据流

### 请求流程

```
User Action
    │
    ▼
Vue Component
    │
    ▼
REST API Call (Axios)
    │
    ▼
REST Controller (Java)
    │
    ▼
Moqui Service Engine
    │
    ▼
Moqui Entity Engine
    │
    ▼
Database
```

### 渲染流程

```
Screen Definition (XML)
    │
    ▼
REST API: /api/v1/screens/{screenPath}
    │
    ▼
Screen Definition Parser
    │
    ▼
JSON Response (Component Tree)
    │
    ▼
Vue Rendering Engine
    │
    ▼
Rendered UI
```

---

## 技术栈

### 后端
- **框架**: Moqui Framework 3.x
- **语言**: Pure Java 21
- **REST**: JAX-RS / Moqui REST
- **文档**: Swagger/OpenAPI 3.0
- **认证**: JWT
- **缓存**: Hazelcast
- **数据库**: PostgreSQL/MySQL/KingBase

### 前端
- **框架**: Vue 3.x
- **语言**: TypeScript
- **UI**: Quasar 2.x (保留现有)
- **路由**: Vue Router 4.x
- **状态**: Pinia
- **HTTP**: Axios
- **桌面**: Electron (可选)

### 开发工具
- **构建**: Gradle (后端), Vite (前端)
- **测试**: JUnit 5, Vitest
- **代码质量**: CheckStyle, ESLint
- **API 测试**: Postman, Swagger UI

---

## 安全设计

### 认证
- JWT Token 认证
- Token 过期时间: 1 小时
- Refresh Token: 7 天
- Token 存储: LocalStorage (前端)

#### API JWT 机制（07-01 Task 5.2 设计落地）

1. **登录与换发流程**
   - 登录端点：`POST /api/v1/auth/login`（公开）
   - 刷新端点：`POST /api/v1/auth/refresh`（公开）
   - 登出端点：`POST /api/v1/auth/logout`（受保护）
   - 登录成功返回 `accessToken`、`refreshToken`、`expiresIn`、`userId`。

2. **请求鉴权流程**
   - API 层通过 `JwtAuthMiddleware` 统一处理 `Authorization: Bearer <token>`。
   - 公开路由（`route.isPublic=true`）跳过鉴权；其余路由默认鉴权。
   - 对过期 token 返回 `401 TOKEN_EXPIRED`；无效 token 返回 `401 AUTHENTICATION_REQUIRED`。

3. **令牌结构与有效期**
   - `JwtUtil.generateTokenPair()` 生成 access/refresh 双令牌，含 `userId`、`type`、`clientIp`、`tokenId` 等 claim。
   - access 令牌默认 60 分钟（可通过 `moqui.jwt.access.expire.minutes` 覆盖）。
   - refresh 令牌默认 30 天（可通过 `moqui.jwt.refresh.expire.days` 覆盖）。

4. **OpenAPI 与开发体验**
   - OpenAPI 全局声明 `BearerAuth`（`type=http`、`scheme=bearer`、`bearerFormat=JWT`）。
   - 公开端点（登录/刷新）在 OpenAPI 中显式标记 `security: []`。

5. **安全边界说明**
   - 当前阶段采用无状态 JWT + 中间件鉴权；会话态能力由 Moqui 原生能力保留。
   - 本 Spec 阶段目标是“REST API 鉴权闭环可运行 + 可文档化”，不引入额外 OAuth2 授权服务器。

### 授权
- RBAC (Role-Based Access Control)
- 权限检查在 Service 层
- API 级别权限控制

#### API RBAC 机制（07-01 Task 5.3 设计落地）

1. **权限模型来源（复用 Moqui）**
   - 角色来源：`moqui.security.UserGroupMember`
   - 角色权限映射：`moqui.security.UserGroupPermission`
   - 用户权限视图：`moqui.security.UserPermissionCheck`

2. **授权执行层次**
   - **API 层**：基于路由公开/受保护属性进行第一层访问控制（未登录直接 401）。
   - **服务层**：调用 Moqui Service 时沿用原有 artifact authz / permission 校验机制，作为 RBAC 主执行层。
   - **实体层**：实体访问继续受 Moqui 内建 authz/filter 规则约束。

3. **错误语义与契约**
   - 认证失败统一 `401`（`AUTHENTICATION_REQUIRED` / `TOKEN_EXPIRED`）。
   - 授权失败沿用 Moqui 语义映射为 `403`（由服务/实体层权限检查触发）。

4. **组织策略**
   - 采用“组件入口 + 服务入口”并存：组件用于可发现性，真实权限边界仍以 Service 定义和 Moqui 权限模型为准。
   - 不在 API 层重复维护一套并行权限表，避免与 Moqui 原生 RBAC 漂移。

### 通信安全
- HTTPS 强制
- CORS 配置
- XSS 防护
- CSRF Token

---

## 性能设计

### 缓存策略
- **实体缓存**: Hazelcast 分布式缓存
- **API 缓存**: HTTP Cache-Control
- **前端缓存**: Service Worker

### 优化措施
- API 响应压缩 (gzip)
- 前端代码分割 (Code Splitting)
- 懒加载 (Lazy Loading)
- 虚拟滚动 (Virtual Scrolling)

---

## 迁移路线图

详见: [design-migration-roadmap.md](design-migration-roadmap.md)

### Phase 1: Groovy → Java (8 周)
- 迁移 88 个 Groovy 文件
- 功能等价验证
- 性能测试

### Phase 2: REST API 层 (12 周)
- 创建 REST API 层
- Swagger 文档化
- 前端 API 客户端

### Phase 3: 前后端分离 (8 周)
- Vue3 前端项目
- 后端渲染 API
- 集成测试

### Phase 4: 渲染引擎适配 (12 周)
- JavaScript 移植
- Vue3 组件映射
- Electron 支持

**总计**: 40 周（10 个月）

---

## 风险和缓解

### 风险 1: 渲染引擎迁移技术可行性
- **缓解**: Phase 4 前进行原型验证
- **备选**: 保留后端渲染 API

### 风险 2: 性能降级
- **缓解**: 每个 Phase 进行性能测试
- **备选**: 性能优化专项

### 风险 3: 兼容性问题
- **缓解**: 充分的回归测试
- **备选**: 新旧系统并行运行

---

## 下一步

1. 审查本设计文档
2. 进行渲染引擎可行性研究
3. 开发原型验证关键技术
4. 获得批准后开始 Phase 1

---

**版本**: 1.0  
**状态**: 设计完成  
**下一步**: 可行性研究和原型开发
