---
name: architecture-design
category: other
description: Template for Architecture Design
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design Document: Moqui Modernization Architecture

## Overview

This design defines a progressive modernization architecture for the Moqui ERP system that preserves its excellent Java framework foundation while replacing problematic components. The approach is strategic: **keep what works, modernize what doesn't**.

**Core Philosophy**: Build on top of Moqui, not replace it. The Moqui framework (Entity Engine, Service Engine, Transaction Management, Cache, Security) is excellent and proven. We add a modern REST API layer on top and migrate problematic Groovy/XML components to Java/OpenAPI.

**Key Architectural Decisions**:
1. **Preserve Moqui Framework**: Keep Entity Engine, Service Engine (Java), Transaction Management, Cache (Hazelcast), Database Abstraction, Security Framework
2. **Add REST API Layer**: Build RESTful API on top of Moqui services, not replace the service layer
3. **Replace Groovy with Java**: Convert 88 Groovy service files to pure Java implementations
4. **Replace XML Services with OpenAPI**: Modern API contracts using Swagger/OpenAPI 3.0
5. **Separate Frontend-Backend**: Complete decoupling via REST API, Vue3/Electron frontend
6. **Migrate Rendering Engine**: Move rendering capabilities to frontend (Vue3 components)
7. **Gradual Migration**: Incremental, phased approach with coexistence and rollback capabilities

## Architecture

### High-Level Architecture

```mermaid
graph TB
    subgraph "Frontend Layer (New)"
        VUE[Vue3 Application]
        ELECTRON[Electron Desktop Shell]
        RENDER[Rendering Engine<br/>Vue3 Components]
        STATE[State Management<br/>Pinia]
    end
    
    subgraph "REST API Layer (New)"
        API[REST API Controllers]
        OPENAPI[OpenAPI 3.0 Specs]
        AUTH[JWT Authentication]
        VALID[Request Validation]
    end
    
    subgraph "Service Layer"
        JAVA_SVC[Java Services<br/>(New)]
        GROOVY_SVC[Groovy Services<br/>(Legacy - To Replace)]
        SVC_ENGINE[Moqui Service Engine<br/>(Preserved)]
    end
    
    subgraph "Moqui Framework (Preserved)"
        ENTITY[Entity Engine]
        TX[Transaction Manager]
        CACHE[Cache System<br/>Hazelcast]
        SEC[Security Framework]
        DB[Database Abstraction]
    end
    
    subgraph "Data Layer"
        POSTGRES[(PostgreSQL)]
    end
    
    subgraph "Component System (Preserved)"
        SCREENS[XML Screen Definitions]
        FORMS[Form/Widget/Section]
        LIBS[Component Libraries<br/>SimpleScreens, HiveMind]
    end
    
    VUE --> RENDER
    VUE --> STATE
    ELECTRON --> VUE
    VUE --> API
    
    API --> OPENAPI
    API --> AUTH
    API --> VALID
    API --> JAVA_SVC
    API --> GROOVY_SVC
    
    JAVA_SVC --> SVC_ENGINE
    GROOVY_SVC --> SVC_ENGINE
    
    SVC_ENGINE --> ENTITY
    SVC_ENGINE --> TX
    SVC_ENGINE --> CACHE
    SVC_ENGINE --> SEC
    
    ENTITY --> DB
    DB --> POSTGRES
    
    RENDER -.->|Interprets| SCREENS
    SCREENS --> FORMS
    FORMS --> LIBS
    
    style ENTITY fill:#90EE90
    style TX fill:#90EE90
    style CACHE fill:#90EE90
    style SEC fill:#90EE90
    style DB fill:#90EE90
    style SVC_ENGINE fill:#90EE90
    style SCREENS fill:#90EE90
    style FORMS fill:#90EE90
    style LIBS fill:#90EE90
    style API fill:#FFD700
    style JAVA_SVC fill:#FFD700
    style VUE fill:#FFD700
    style RENDER fill:#FFD700
    style GROOVY_SVC fill:#FFB6C1
```

**Legend**:
- 🟢 Green: Preserved (Excellent Moqui components)
- 🟡 Yellow: New (Modernization additions)
- 🌸 Pink: Legacy (To be replaced)

### Layered Architecture

The architecture follows a clear layered approach:

1. **Frontend Layer** (New - Vue3/Electron)
   - User interface components
   - Rendering engine (migrated from backend)
   - State management
   - API client

2. **REST API Layer** (New - Built on Moqui)
   - RESTful endpoints
   - OpenAPI specifications
   - Authentication/Authorization
   - Request/Response validation

3. **Service Layer** (Mixed - Java replacing Groovy)
   - Business logic services
   - Service orchestration
   - Moqui Service Engine (preserved)

4. **Framework Layer** (Preserved - Moqui Core)
   - Entity Engine
   - Transaction Management
   - Cache System
   - Security Framework
   - Database Abstraction

5. **Data Layer** (Preserved)
   - PostgreSQL database
   - Entity definitions

6. **Component System** (Preserved - Leveraged by Frontend)
   - XML Screen definitions
   - Form/Widget/Section components
   - Component libraries

## Components and Interfaces

### 1. REST API Layer

**Purpose**: Provide modern RESTful API on top of Moqui services, enabling frontend-backend separation.

**Key Components**:

#### 1.1 API Controllers
```java
// Example: Order API Controller
@RestController
@RequestMapping("/api/v1/orders")
public class OrderApiController {
    
    @Autowired
    private OrderService orderService; // Java service wrapping Moqui
    
    @PostMapping
    public ResponseEntity<OrderResponse> createOrder(
        @Valid @RequestBody CreateOrderRequest request) {
        
        // Delegate to Moqui service through Java wrapper
        OrderResponse response = orderService.createOrder(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(response);
    }
    
    @GetMapping("/{orderId}")
    public ResponseEntity<OrderResponse> getOrder(
        @PathVariable String orderId) {
        
        OrderResponse response = orderService.getOrder(orderId);
        return ResponseEntity.ok(response);
    }
}
```

#### 1.2 OpenAPI Specifications
```yaml
# Example: Order API OpenAPI Spec
openapi: 3.0.0
info:
  title: Moqui ERP Order API
  version: 1.0.0
paths:
  /api/v1/orders:
    post:
      summary: Create a new order
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateOrderRequest'
      responses:
        '201':
          description: Order created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OrderResponse'
        '400':
          description: Invalid request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
components:
  schemas:
    CreateOrderRequest:
      type: object
      required:
        - customerId
        - items
      properties:
        customerId:
          type: string
        items:
          type: array
          items:
            $ref: '#/components/schemas/OrderItem'
```

#### 1.3 Authentication & Authorization
```java
// JWT-based authentication
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) {
        
        String token = extractToken(request);
        if (token != null && jwtValidator.validate(token)) {
            // Integrate with Moqui Security Framework
            UserDetails userDetails = moquiSecurityService.loadUser(token);
            Authentication auth = new UsernamePasswordAuthenticationToken(
                userDetails, null, userDetails.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        
        filterChain.doFilter(request, response);
    }
}
```

**Interfaces**:
- **Input**: HTTP requests (JSON) from frontend
- **Output**: HTTP responses (JSON) to frontend
- **Integration**: Calls Moqui services through Java service wrappers

### 2. Java Service Layer

**Purpose**: Replace Groovy services with pure Java implementations that wrap Moqui Service Engine.

**Key Components**:

#### 2.1 Service Interface Pattern
```java
// Service interface
public interface OrderService {
    OrderResponse createOrder(CreateOrderRequest request);
    OrderResponse getOrder(String orderId);
    List<OrderResponse> listOrders(OrderFilter filter);
    void cancelOrder(String orderId);
}

// Implementation wrapping Moqui
@Service
public class OrderServiceImpl implements OrderService {
    
    @Autowired
    private ExecutionContextFactory ecf; // Moqui context factory
    
    @Override
    public OrderResponse createOrder(CreateOrderRequest request) {
        ExecutionContext ec = ecf.getExecutionContext();
        
        // Call Moqui service through Service Engine
        Map<String, Object> result = ec.getService()
            .sync()
            .name("mantle.order.OrderServices.create#Order")
            .parameters(convertToMoquiParams(request))
            .call();
        
        return convertToResponse(result);
    }
    
    private Map<String, Object> convertToMoquiParams(CreateOrderRequest request) {
        // Convert REST request to Moqui service parameters
        Map<String, Object> params = new HashMap<>();
        params.put("customerId", request.getCustomerId());
        params.put("items", request.getItems());
        return params;
    }
    
    private OrderResponse convertToResponse(Map<String, Object> result) {
        // Convert Moqui service result to REST response
        return OrderResponse.builder()
            .orderId((String) result.get("orderId"))
            .status((String) result.get("statusId"))
            .build();
    }
}
```

#### 2.2 Groovy to Java Conversion Pattern

**Current Groovy Service** (example):
```groovy
// framework/src/main/groovy/org/moqui/impl/service/runner/GroovyScriptRunner.groovy
def createOrder(ExecutionContext ec) {
    def orderId = ec.entity.sequencedIdPrimary("mantle.order.OrderHeader", null, null)
    ec.entity.makeValue("mantle.order.OrderHeader")
        .set("orderId", orderId)
        .set("customerId", ec.context.customerId)
        .create()
    return [orderId: orderId]
}
```

**Converted Java Service**:
```java
@Service
public class OrderCreationService {
    
    @Autowired
    private ExecutionContextFactory ecf;
    
    public Map<String, Object> createOrder(Map<String, Object> context) {
        ExecutionContext ec = ecf.getExecutionContext();
        
        // Generate sequenced ID
        String orderId = ec.getEntity()
            .sequencedIdPrimary("mantle.order.OrderHeader", null, null);
        
        // Create entity value
        ec.getEntity()
            .makeValue("mantle.order.OrderHeader")
            .set("orderId", orderId)
            .set("customerId", context.get("customerId"))
            .create();
        
        Map<String, Object> result = new HashMap<>();
        result.put("orderId", orderId);
        return result;
    }
}
```

**Conversion Rules**:
1. Groovy dynamic typing → Java strong typing
2. Groovy closures → Java lambdas or methods
3. Groovy GString → Java String formatting
4. Groovy collection shortcuts → Java Stream API
5. Groovy Elvis operator → Java Optional or ternary
6. Preserve Moqui API calls exactly

### 3. Frontend Rendering Engine

**Purpose**: Migrate Moqui's rendering capabilities to Vue3 frontend while preserving functionality.

**Key Components**:

#### 3.1 Screen Definition Interpreter
```typescript
// Vue3 composable for interpreting XML Screen definitions
export function useScreenRenderer() {
    const renderScreen = async (screenPath: string) => {
        // Fetch screen definition from backend API
        const screenDef = await api.get(`/api/v1/screens/${screenPath}`);
        
        // Parse XML screen definition
        const parsed = parseScreenXml(screenDef.xml);
        
        // Convert to Vue3 component tree
        return buildComponentTree(parsed);
    };
    
    const buildComponentTree = (screenDef: ScreenDefinition) => {
        // Map screen sections to Vue components
        return {
            component: resolveComponent(screenDef.type),
            props: extractProps(screenDef),
            children: screenDef.children.map(buildComponentTree)
        };
    };
    
    return { renderScreen };
}
```

#### 3.2 Form/Widget Component Mapping
```typescript
// Map Moqui form widgets to Vue3 components
const widgetComponentMap = {
    'text-line': TextInput,
    'text-area': TextArea,
    'drop-down': SelectDropdown,
    'date-time': DateTimePicker,
    'check': Checkbox,
    'radio': RadioButton,
    'submit': SubmitButton
};

// Vue3 component for rendering Moqui forms
export const MoquiForm = defineComponent({
    props: {
        formDefinition: Object as PropType<FormDefinition>
    },
    setup(props) {
        const formData = reactive({});
        
        const renderField = (field: FieldDefinition) => {
            const Component = widgetComponentMap[field.widgetType];
            return h(Component, {
                modelValue: formData[field.name],
                'onUpdate:modelValue': (value) => {
                    formData[field.name] = value;
                },
                ...field.props
            });
        };
        
        return () => h('form', 
            props.formDefinition.fields.map(renderField)
        );
    }
});
```

#### 3.3 FreeMarker to Vue3 Template Conversion

**Current FreeMarker Macro** (example):
```ftl
<#macro renderField field>
    <div class="form-field">
        <label>${field.label}</label>
        <#if field.type == "text">
            <input type="text" name="${field.name}" value="${field.value!}"/>
        <#elseif field.type == "select">
            <select name="${field.name}">
                <#list field.options as option>
                    <option value="${option.value}">${option.label}</option>
                </#list>
            </select>
        </#if>
    </div>
</#macro>
```

**Converted Vue3 Component**:
```vue
<template>
    <div class="form-field">
        <label>{{ field.label }}</label>
        <input v-if="field.type === 'text'" 
               type="text" 
               :name="field.name" 
               v-model="fieldValue"/>
        <select v-else-if="field.type === 'select'" 
                :name="field.name" 
                v-model="fieldValue">
            <option v-for="option in field.options" 
                    :key="option.value" 
                    :value="option.value">
                {{ option.label }}
            </option>
        </select>
    </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

const props = defineProps<{
    field: FieldDefinition
}>();

const fieldValue = ref(props.field.value || '');
</script>
```

#### 3.4 Multi-Format Output Support
```typescript
// Support for CSV, XML, QVT export formats
export class OutputFormatService {
    
    exportToCsv(data: any[], columns: string[]): Blob {
        const csv = [
            columns.join(','),
            ...data.map(row => columns.map(col => row[col]).join(','))
        ].join('\n');
        
        return new Blob([csv], { type: 'text/csv' });
    }
    
    exportToXml(data: any[], rootElement: string): Blob {
        const xml = `<?xml version="1.0"?>
<${rootElement}>
${data.map(item => this.objectToXml(item)).join('\n')}
</${rootElement}>`;
        
        return new Blob([xml], { type: 'application/xml' });
    }
    
    exportToQvt(data: any[]): Blob {
        // QVT (QlikView Table) format
        const qvt = this.convertToQvtFormat(data);
        return new Blob([qvt], { type: 'application/qvt' });
    }
}
```

### 4. Vue3/Electron Integration

**Purpose**: Provide modern desktop application shell with Vue3 frontend.

**Key Components**:

#### 4.1 Electron Main Process
```typescript
// main.ts - Electron main process
import { app, BrowserWindow, ipcMain } from 'electron';

let mainWindow: BrowserWindow;

app.on('ready', () => {
    mainWindow = new BrowserWindow({
        width: 1200,
        height: 800,
        webPreferences: {
            nodeIntegration: false,
            contextIsolation: true,
            preload: path.join(__dirname, 'preload.js')
        }
    });
    
    mainWindow.loadURL('http://localhost:5173'); // Vite dev server
});

// IPC handlers for native functionality
ipcMain.handle('save-file', async (event, data) => {
    const { dialog } = require('electron');
    const result = await dialog.showSaveDialog({
        filters: [{ name: 'CSV', extensions: ['csv'] }]
    });
    
    if (!result.canceled) {
        fs.writeFileSync(result.filePath, data);
    }
});
```

#### 4.2 API Client Architecture
```typescript
// api-client.ts - HTTP client for REST API
import axios, { AxiosInstance } from 'axios';

export class ApiClient {
    private client: AxiosInstance;
    private authToken: string | null = null;
    
    constructor(baseURL: string) {
        this.client = axios.create({
            baseURL,
            timeout: 30000,
            headers: {
                'Content-Type': 'application/json'
            }
        });
        
        // Request interceptor for auth token
        this.client.interceptors.request.use(config => {
            if (this.authToken) {
                config.headers.Authorization = `Bearer ${this.authToken}`;
            }
            return config;
        });
        
        // Response interceptor for error handling
        this.client.interceptors.response.use(
            response => response,
            error => this.handleError(error)
        );
    }
    
    async login(username: string, password: string): Promise<void> {
        const response = await this.client.post('/api/v1/auth/login', {
            username,
            password
        });
        this.authToken = response.data.token;
    }
    
    async get<T>(url: string): Promise<T> {
        const response = await this.client.get<T>(url);
        return response.data;
    }
    
    async post<T>(url: string, data: any): Promise<T> {
        const response = await this.client.post<T>(url, data);
        return response.data;
    }
    
    private handleError(error: any): Promise<never> {
        if (error.response?.status === 401) {
            // Handle authentication error
            this.authToken = null;
            // Redirect to login
        }
        return Promise.reject(error);
    }
}
```

#### 4.3 State Management (Pinia)
```typescript
// stores/order-store.ts
import { defineStore } from 'pinia';
import { apiClient } from '@/services/api-client';

export const useOrderStore = defineStore('order', {
    state: () => ({
        orders: [] as Order[],
        currentOrder: null as Order | null,
        loading: false,
        error: null as string | null
    }),
    
    actions: {
        async fetchOrders(filter?: OrderFilter) {
            this.loading = true;
            try {
                this.orders = await apiClient.get('/api/v1/orders', {
                    params: filter
                });
            } catch (error) {
                this.error = error.message;
            } finally {
                this.loading = false;
            }
        },
        
        async createOrder(request: CreateOrderRequest) {
            this.loading = true;
            try {
                const order = await apiClient.post('/api/v1/orders', request);
                this.orders.push(order);
                return order;
            } catch (error) {
                this.error = error.message;
                throw error;
            } finally {
                this.loading = false;
            }
        }
    }
});
```

## Data Models

### REST API Request/Response Models

The REST API uses JSON for all request and response payloads. Data models are defined using JSON Schema in OpenAPI specifications.

#### Example: Order Domain Models

**CreateOrderRequest**:
```json
{
  "customerId": "string",
  "orderDate": "2024-01-15T10:30:00Z",
  "currencyUomId": "USD",
  "items": [
    {
      "productId": "string",
      "quantity": 10,
      "unitPrice": 99.99
    }
  ],
  "shippingAddress": {
    "address1": "string",
    "city": "string",
    "stateProvinceGeoId": "string",
    "postalCode": "string",
    "countryGeoId": "string"
  }
}
```

**OrderResponse**:
```json
{
  "orderId": "string",
  "orderName": "string",
  "statusId": "OrderPlaced",
  "customerId": "string",
  "orderDate": "2024-01-15T10:30:00Z",
  "grandTotal": 999.90,
  "currencyUomId": "USD",
  "items": [
    {
      "orderItemSeqId": "00001",
      "productId": "string",
      "itemDescription": "string",
      "quantity": 10,
      "unitPrice": 99.99,
      "itemTotal": 999.90
    }
  ],
  "shippingAddress": {
    "contactMechId": "string",
    "address1": "string",
    "city": "string",
    "stateProvinceGeoId": "string",
    "postalCode": "string",
    "countryGeoId": "string"
  }
}
```

### Moqui Entity Models (Preserved)

The underlying Moqui entity models remain unchanged. The REST API layer provides a translation between REST JSON models and Moqui entity models.

**Example Entity Mapping**:
```java
// Convert REST model to Moqui entities
public void createOrderEntities(CreateOrderRequest request, ExecutionContext ec) {
    // Create OrderHeader entity
    EntityValue orderHeader = ec.getEntity()
        .makeValue("mantle.order.OrderHeader")
        .set("customerId", request.getCustomerId())
        .set("orderDate", request.getOrderDate())
        .set("currencyUomId", request.getCurrencyUomId())
        .setSequencedIdPrimary()
        .create();
    
    String orderId = orderHeader.getString("orderId");
    
    // Create OrderItem entities
    int seqId = 1;
    for (OrderItemRequest item : request.getItems()) {
        ec.getEntity()
            .makeValue("mantle.order.OrderItem")
            .set("orderId", orderId)
            .set("orderItemSeqId", String.format("%05d", seqId++))
            .set("productId", item.getProductId())
            .set("quantity", item.getQuantity())
            .set("unitPrice", item.getUnitPrice())
            .create();
    }
    
    // Create PostalAddress and OrderContactMech entities
    // ... (similar pattern)
}
```

## Migration Strategy

### Phase 1: Foundation (Weeks 1-2)

**Objective**: Set up REST API infrastructure and convert first service

**Tasks**:
1. Set up Spring Boot REST API project structure
2. Configure integration with Moqui framework
3. Implement JWT authentication integration with Moqui Security
4. Create OpenAPI specification template
5. Convert one simple Groovy service to Java (proof of concept)
6. Implement first REST endpoint
7. Set up automated testing framework

**Deliverables**:
- Working REST API skeleton
- Authentication working
- One service converted and exposed via REST
- Testing framework in place

**Success Criteria**:
- REST API can authenticate users via Moqui Security
- One service callable via REST endpoint
- All tests passing

### Phase 2: Core Services Migration (Weeks 3-6)

**Objective**: Convert high-priority Groovy services to Java and expose via REST API

**Priority Services** (based on usage frequency):
1. Order management services (create, update, cancel)
2. Product catalog services (search, details)
3. Customer management services (create, update, search)
4. Inventory services (check availability, reserve)
5. Invoice services (create, finalize)

**Tasks per Service**:
1. Analyze Groovy service implementation
2. Design Java service interface
3. Implement Java service wrapping Moqui
4. Create OpenAPI specification
5. Implement REST controller
6. Write unit tests and property tests
7. Write integration tests
8. Performance testing

**Coexistence Strategy**:
- Both Groovy and Java services coexist
- REST API routes to Java services when available
- Fallback to Groovy services for unconverted services
- Gradual cutover as Java services are validated

### Phase 3: Frontend Foundation (Weeks 7-10)

**Objective**: Build Vue3/Electron frontend foundation and rendering engine

**Tasks**:
1. Set up Vue3 + Vite + TypeScript project
2. Set up Electron shell
3. Implement API client with authentication
4. Implement state management (Pinia stores)
5. Implement routing (Vue Router)
6. Create base UI component library
7. Implement screen definition interpreter (XML → Vue3)
8. Implement form/widget component mapping
9. Convert key FreeMarker macros to Vue3 components
10. Implement multi-format export (CSV, XML, QVT)

**Proof of Concept**:
- Render one complete Moqui screen in Vue3
- Validate rendering fidelity
- Validate performance

### Phase 4: Frontend-Backend Integration (Weeks 11-14)

**Objective**: Integrate Vue3 frontend with REST API backend

**Tasks**:
1. Implement order management UI
2. Implement product catalog UI
3. Implement customer management UI
4. Implement inventory management UI
5. Implement invoice management UI
6. End-to-end testing
7. Performance optimization
8. User acceptance testing

**Validation**:
- All core workflows functional
- Performance meets or exceeds current system
- User feedback positive

### Phase 5: Remaining Services Migration (Weeks 15-18)

**Objective**: Convert remaining Groovy services to Java

**Tasks**:
1. Convert remaining 60+ Groovy services
2. Update OpenAPI specifications
3. Update REST controllers
4. Update frontend to use new endpoints
5. Comprehensive testing
6. Performance tuning

### Phase 6: Cleanup and Optimization (Weeks 19-20)

**Objective**: Remove legacy code, optimize, and finalize

**Tasks**:
1. Remove Groovy service files
2. Remove XML service definitions
3. Remove backend rendering code
4. Optimize REST API performance
5. Optimize frontend performance
6. Final security audit
7. Final testing
8. Documentation
9. Deployment preparation

**Deliverables**:
- Clean codebase (no Groovy, no XML services, no backend rendering)
- Optimized performance
- Complete documentation
- Production-ready system

### Rollback Strategy

Each phase has a rollback plan:

**Phase 1-2 Rollback**:
- Disable REST API layer
- Continue using existing Moqui services directly
- No data migration needed

**Phase 3-4 Rollback**:
- Revert to existing Moqui frontend
- REST API remains available but unused
- No data migration needed

**Phase 5-6 Rollback**:
- Restore Groovy service files from version control
- Restore XML service definitions
- Restore backend rendering code
- Requires code deployment rollback

### Risk Mitigation

**Technical Risks**:

1. **Rendering Engine Migration Complexity**
   - Risk: Vue3 cannot replicate all FreeMarker/Macro functionality
   - Mitigation: Proof of concept in Phase 3, validate before committing
   - Contingency: Keep backend rendering as fallback option

2. **Performance Degradation**
   - Risk: REST API adds latency compared to direct service calls
   - Mitigation: Performance testing in each phase, optimize API layer
   - Contingency: Implement caching, connection pooling, async processing

3. **Groovy-to-Java Conversion Errors**
   - Risk: Semantic differences between Groovy and Java cause bugs
   - Mitigation: Comprehensive testing (unit + property + integration)
   - Contingency: Parallel run Groovy and Java, compare results

4. **Integration Issues with Moqui**
   - Risk: REST API layer doesn't integrate cleanly with Moqui
   - Mitigation: Early proof of concept, close collaboration with Moqui experts
   - Contingency: Adjust architecture based on findings

**Business Risks**:

1. **Extended Timeline**
   - Risk: 20-week timeline extends due to unforeseen issues
   - Mitigation: Buffer time in each phase, prioritize critical services
   - Contingency: Deliver in increments, defer non-critical services

2. **User Adoption**
   - Risk: Users resist new frontend interface
   - Mitigation: User involvement in design, training, gradual rollout
   - Contingency: Provide both interfaces during transition period

3. **Data Integrity**
   - Risk: Migration causes data corruption or loss
   - Mitigation: No data migration needed (same database), comprehensive testing
   - Contingency: Database backups before each phase



## Ontology Model (Backfilled)

### Entities
- **ArchitectureDesignRecord**: Core domain record for Architecture Design scenarios.
- **ArchitectureDesignProcess**: Process context handling lifecycle transitions.
- **ArchitectureDesignAuditEvent**: Immutable operation/audit trace entry.

### Relations
- **ArchitectureDesignRecord** 1:N **ArchitectureDesignProcess** (lifecycle orchestration).
- **ArchitectureDesignProcess** 1:N **ArchitectureDesignAuditEvent** (traceability and compliance).

### Business Rules
- **BR-001**: Mandatory fields must pass validation before persistence.
- **BR-002**: State transitions must comply with lifecycle policy.
- **BR-003**: Every mutating operation must emit an audit event.

### Decision Logic
- **DL-001**: If record does not exist, route to create flow; otherwise update flow.
- **DL-002**: If requested transition is invalid, reject and return violation reason.
- **DL-003**: If post-check fails, rollback and mark operation as failed.
