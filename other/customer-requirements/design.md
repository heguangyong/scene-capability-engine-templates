---
name: customer-requirements
category: other
description: Template for Customer Requirements
tags: []
author: FallingAKS
created_at: '2026-02-18'
updated_at: '2026-02-18'
version: 1.0.0
kse_version: 3.0.2
---

# Design Document: 国产化ERP系统POC测试

## Overview

This design document specifies the architecture and technical approach for implementing a domestically-produced ERP system that meets comprehensive manufacturing requirements. The system follows a modular, microservices-based architecture supporting production planning, procurement, inventory, cost management, and service operations in a fully domestic IT environment.

### Design Goals

1. **Modularity**: Independent modules for each business domain (sales, production, procurement, etc.)
2. **Scalability**: Microservices architecture supporting horizontal scaling
3. **Domestic Compliance**: Full operation on domestic IT infrastructure without performance degradation
4. **Integration**: Open APIs and standard interfaces for system integration
5. **Extensibility**: Low-code and high-code development capabilities for customization
6. **Performance**: High availability, load balancing, and efficient batch processing
7. **Security**: Three-role management, encryption, and audit capabilities

### Technology Stack

- **Backend**: Java-based microservices with Spring Boot/Spring Cloud
- **Frontend**: Vue 3 + TypeScript for responsive web UI
- **Database**: Domestic database (e.g., DM, KingBase, GaussDB) with clustering support
- **Message Queue**: Domestic message broker for asynchronous processing
- **API Gateway**: RESTful and Web Service support
- **Cache**: Distributed caching for performance optimization
- **Deployment**: Kubernetes for container orchestration, supporting blue-green deployment


## Architecture

### System Architecture

The system follows a layered microservices architecture:

```
┌─────────────────────────────────────────────────────────────┐
│                     Presentation Layer                       │
│  (Vue 3 Web UI, Mobile Apps, Third-party Integrations)      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                       │
│     (Authentication, Rate Limiting, Routing, Logging)       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                   Business Services Layer                    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │  Master  │ │  Sales   │ │Production│ │Procurement│      │
│  │   Data   │ │  Mgmt    │ │ Planning │ │   Mgmt   │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │Inventory │ │   Cost   │ │ Project  │ │Equipment │      │
│  │   Mgmt   │ │   Mgmt   │ │   Mgmt   │ │   Mgmt   │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                   Platform Services Layer                    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │  User &  │ │ Workflow │ │  Report  │ │   Audit  │      │
│  │   Auth   │ │  Engine  │ │  Engine  │ │  Logging │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      Data Access Layer                       │
│     (ORM, Connection Pooling, Transaction Management)       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    Data Storage Layer                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │ Business │ │  Cache   │ │  File    │ │  Backup  │      │
│  │    DB    │ │  (Redis) │ │ Storage  │ │  Storage │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### Deployment Architecture

- **Multi-tier Deployment**: Separate tiers for web, application, and database
- **Cluster Configuration**: Multiple nodes per tier for high availability
- **Load Balancing**: Hardware or software load balancers distributing traffic
- **Blue-Green Deployment**: Zero-downtime upgrades with environment switching
- **Hot Standby**: Database replication with automatic failover


## Components and Interfaces

### 1. Master Data Management Module

**Responsibilities:**
- Material master data CRUD operations with multi-organizational support
- BOM management with multi-level structures, variants, and co-products
- Routing management with work centers and operations
- Engineering change management (ECN) with impact analysis
- Customer and supplier master data
- Financial master data (cost elements, cost centers, GL accounts)

**Key Interfaces:**
- `MaterialService`: Material CRUD, status management, batch/serial number configuration
- `BOMService`: BOM CRUD, copy, comparison, where-used queries
- `RoutingService`: Routing CRUD, work center assignment, time standards
- `ECNService`: Change request processing, impact calculation, execution tracking

**Data Models:**
- Material: code, descriptions, attributes by organization, status, planning parameters
- BOM: header (material, type, version), items (component, quantity, position, substitutes)
- Routing: header (material, version), operations (sequence, work center, times, cost center)
- ECN: change number, affected objects, effective dates, execution status

### 2. Sales Management Module

**Responsibilities:**
- Sales order lifecycle management (create, modify, approve, close)
- Delivery processing with picking and goods issue
- Pricing and conditions management
- Sales order BOM configuration
- Integration with production and procurement

**Key Interfaces:**
- `SalesOrderService`: Order CRUD, approval workflow, status management
- `DeliveryService`: Delivery creation, picking, goods issue posting
- `PricingService`: Price determination, discount calculation
- `SalesIntegrationService`: Demand transfer to production/procurement

**Data Models:**
- SalesOrder: header (customer, dates, terms), items (material, quantity, price, delivery)
- Delivery: header (order reference, ship-to), items (material, quantity, batch, picking status)
- PricingCondition: condition type, value, validity period, customer/material assignment


### 3. Production Planning and Execution Module

**Responsibilities:**
- Demand management (independent requirements, reservations)
- MPS/MRP calculation with multiple planning strategies
- Production order management
- Material availability checking and reservation
- Production reporting and goods receipt
- Capacity planning and load balancing

**Key Interfaces:**
- `DemandService`: Independent demand and reservation management
- `MPSService`: Master production schedule generation and approval
- `MRPService`: Material requirements planning calculation
- `ProductionOrderService`: Order CRUD, release, confirmation
- `CapacityService`: Capacity analysis and load balancing

**Data Models:**
- IndependentDemand: material, quantity, date, MRP area, version, active status
- PlanningStrategy: strategy code (10, 11, 40, 50), parameters, applicability
- ProductionOrder: header (material, quantity, dates, status), BOM, routing, costs
- PlannedOrder: material, quantity, dates, MRP controller, conversion status
- CapacityRequirement: work center, date, required hours, available hours

### 4. Procurement and Inventory Module

**Responsibilities:**
- Purchase requisition and order management
- Goods receipt and invoice verification
- Inventory management with batch and serial numbers
- Subcontracting and consignment processing
- Supplier evaluation and price management

**Key Interfaces:**
- `PurchaseRequisitionService`: Requisition generation from MRP, manual creation
- `PurchaseOrderService`: PO CRUD, approval, monitoring
- `GoodsReceiptService`: Receipt posting, quality inspection, inventory update
- `InvoiceVerificationService`: Three-way matching, tolerance checking
- `InventoryService`: Stock movements, transfers, batch management

**Data Models:**
- PurchaseRequisition: material, quantity, delivery date, source assignment
- PurchaseOrder: header (supplier, terms), items (material, quantity, price, delivery)
- MaterialDocument: movement type, material, quantity, batch, storage location, GL impact
- Batch: batch number, material, quantity, supplier batch, quality status, characteristics
- SerialNumber: serial number, material, equipment reference, status


### 5. Cost Management Module

**Responsibilities:**
- Standard cost calculation and maintenance
- Actual cost collection on production orders
- Cost allocation and distribution
- Variance analysis
- Material ledger settlement

**Key Interfaces:**
- `StandardCostService`: Cost estimation, BOM costing, price updates
- `ActualCostService`: Cost collection, allocation, settlement
- `VarianceService`: Variance calculation and analysis
- `MaterialLedgerService`: Material price determination, period-end closing

**Data Models:**
- StandardCost: material, cost components (material, labor, overhead), validity period
- CostElement: element code, description, category, GL account assignment
- ProductionOrderCost: order reference, planned costs, actual costs, variances
- CostAllocation: source object, target object, allocation rule, amount

### 6. Platform Services

**Responsibilities:**
- User authentication and authorization
- Role-based access control with three-role management
- Workflow engine for approval processes
- Report generation and export
- Audit logging and compliance
- System configuration and customization

**Key Interfaces:**
- `AuthenticationService`: Login, SSO, password management
- `AuthorizationService`: Permission checking, role assignment
- `WorkflowService`: Process definition, instance execution, task management
- `ReportService`: Report generation, parameter input, export
- `AuditService`: Event logging, query, compliance reporting
- `ConfigurationService`: Parameter maintenance, feature toggles

**Data Models:**
- User: user ID, name, roles, organizational assignments, status
- Role: role code, permissions, description
- WorkflowDefinition: process ID, steps, conditions, approvers
- WorkflowInstance: definition reference, current step, history, status
- AuditLog: timestamp, user, action, object type, object ID, before/after values


## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Material Code Length Flexibility

*For any* material code string with length greater than 18 characters, the system should successfully create and store the material master record without truncation or error.

**Validates: Requirements 1.1**

### Property 2: Material Status Enforcement

*For any* material marked with status "disabled" or "obsolete", attempts to create new transactions (sales orders, production orders, purchase orders) referencing that material should be rejected with appropriate error messages.

**Validates: Requirements 1.3**

### Property 3: BOM Multi-Level Explosion

*For any* multi-level BOM structure, exploding the top-level material should recursively include all components at all levels with correct quantities calculated by multiplying parent-child ratios through the hierarchy.

**Validates: Requirements 1.4**

### Property 4: Material Substitution Selection

*For any* material with defined substitutes, when the primary material is unavailable and a substitute is selected, the system should choose the substitute with highest priority, and if multiple substitutes have equal priority, allocate according to defined ratios.

**Validates: Requirements 1.5**

### Property 5: ECN Impact Identification

*For any* engineering change affecting a BOM or routing, the ECN impact analysis should identify all open sales orders, purchase requisitions, production orders, and inventory that reference the changed object.

**Validates: Requirements 2.1**

### Property 6: Sales Order Validation

*For any* sales order creation attempt, if required fields (customer, material, quantity, delivery date) are missing or invalid (e.g., delivery date in past, negative quantity), the system should reject the order with specific validation error messages.

**Validates: Requirements 3.1**

### Property 7: Delivery Inventory Availability

*For any* delivery creation attempt, if the sum of requested quantities exceeds available inventory (considering existing reservations), the system should either reject the delivery or create it with availability warning.

**Validates: Requirements 3.2**

### Property 8: Goods Issue Inventory Reduction

*For any* goods issue posting, the inventory quantity after posting should equal the inventory quantity before posting minus the issued quantity, maintaining this invariant across all storage locations and batches.

**Validates: Requirements 3.3**


### Property 9: MRP Planned Order Generation

*For any* independent demand with associated BOM, MRP calculation should generate planned orders for all required components with quantities equal to demand quantity multiplied by BOM component quantities, adjusted for existing inventory and scheduled receipts.

**Validates: Requirements 4.1**

### Property 10: Production Order Material Availability

*For any* production order, the material availability check should flag the order as "not available" if any required component (from order BOM) has insufficient unreserved inventory to cover the order quantity.

**Validates: Requirements 4.2**

### Property 11: Production Confirmation Inventory Update

*For any* production order confirmation, the finished goods inventory should increase by the confirmed quantity, and component inventory should decrease by the quantities specified in the order BOM multiplied by the confirmed quantity.

**Validates: Requirements 4.3**

### Property 12: MRP Purchase Requisition Generation

*For any* purchased material with net requirements (gross requirements minus available inventory minus scheduled receipts), MRP should generate purchase requisitions with quantities covering the net requirements, respecting lot sizing rules.

**Validates: Requirements 5.1**

### Property 13: Invoice Three-Way Matching

*For any* invoice verification, the invoice should be accepted only if: (1) invoice quantity matches goods receipt quantity within tolerance, (2) invoice price matches purchase order price within tolerance, and (3) all three documents reference the same purchase order line item.

**Validates: Requirements 5.2**

### Property 14: Batch FIFO Consumption

*For any* material issue transaction for a batch-managed material, when multiple batches are available, the system should select the batch with the earliest goods receipt date (FIFO strategy) unless explicitly overridden.

**Validates: Requirements 5.3**

### Property 15: Batch Traceability Chain

*For any* batch of a finished good, tracing upstream should identify all component batches consumed in its production, and tracing downstream should identify all parent batches or sales deliveries where it was consumed or shipped.

**Validates: Requirements 5.4**

### Property 16: Standard Cost Calculation Accuracy

*For any* material with BOM and routing, the calculated standard cost should equal the sum of (component standard costs × BOM quantities) plus (operation times × activity rates) for all components and operations.

**Validates: Requirements 6.1**

### Property 17: Cost Variance Calculation

*For any* production order at settlement, the variance for each cost element should equal the actual cost minus the standard cost for that element, with total variance equaling sum of all element variances.

**Validates: Requirements 6.2**

### Property 18: Project Cost Accumulation

*For any* project with assigned production orders and purchase orders, the total project cost should equal the sum of all actual costs posted to those orders, maintaining this invariant as new costs are posted.

**Validates: Requirements 7.1**

### Property 19: Workflow Approval Progression

*For any* workflow instance, the instance should progress through approval steps in the defined sequence, and should not reach "approved" status until all required approvers have approved at their respective steps.

**Validates: Requirements 14.1**


## Error Handling

### Error Code Management

Every error condition in the system must have a unique error code following the pattern: `MODULE-CATEGORY-SEQUENCE` (e.g., `MAT-VAL-001` for material validation error #1).

**Error Code Structure:**
- Module prefix (3 chars): MAT, BOM, SLS, PRD, PRC, INV, CST, PRJ, etc.
- Category (3 chars): VAL (validation), BUS (business logic), SYS (system), INT (integration)
- Sequence (3 digits): 001-999

**Error Instance Logging:**
Each error occurrence must log:
- Unique instance ID (UUID)
- Error code
- User ID, computer name, IP address
- Timestamp
- Context data (e.g., document numbers, key values)
- SQL queries for data validation errors
- Comparison values for quantity/amount validation errors

**Error Resolution Guide:**
Each error code must have documented:
- Error description
- Possible causes
- Step-by-step troubleshooting procedure
- Resolution actions
- Database tables involved

### Exception Handling Strategy

**Validation Errors:**
- Caught at service layer
- Return structured error response with error code and user-friendly message
- Log to error instance table
- Do not rollback transaction (no data changed yet)

**Business Logic Errors:**
- Caught at service layer
- Rollback transaction if data was modified
- Return error code and message
- Log with full context

**System Errors:**
- Caught at infrastructure layer
- Rollback transaction
- Log with stack trace
- Return generic error message to user (hide internal details)
- Alert system administrators

**Integration Errors:**
- Retry logic for transient failures (3 attempts with exponential backoff)
- Dead letter queue for persistent failures
- Detailed logging of request/response payloads
- Alert integration monitoring team


## Testing Strategy

### Dual Testing Approach

The system requires both unit testing and property-based testing for comprehensive quality assurance:

**Unit Tests:**
- Verify specific examples and edge cases
- Test error conditions and boundary values
- Validate integration points between modules
- Focus on concrete scenarios with known inputs/outputs

**Property-Based Tests:**
- Verify universal properties across all inputs
- Use randomized test data generation
- Validate invariants and business rules
- Ensure correctness at scale

Both approaches are complementary and necessary. Unit tests catch specific bugs, while property tests verify general correctness.

### Property-Based Testing Configuration

**Framework Selection:**
- Java: Use **JUnit-Quickcheck** or **jqwik** for property-based testing
- Minimum 100 iterations per property test (configurable based on complexity)

**Test Tagging Convention:**
Each property test must include a comment referencing the design property:
```java
/**
 * Feature: {{SPEC_NAME}}
 * Property 8: Goods Issue Inventory Reduction
 * 
 * For any goods issue posting, inventory quantity after should equal
 * inventory before minus issued quantity.
 */
@Property
void testGoodsIssueInventoryReduction(@ForAll Material material, 
                                       @ForAll @Positive int quantity) {
    // Test implementation
}
```

### Test Coverage Requirements

**Master Data Module:**
- Unit tests: Material CRUD operations, BOM explosion, routing validation
- Property tests: Properties 1-5 (material code length, status enforcement, BOM explosion, substitution, ECN impact)

**Sales Module:**
- Unit tests: Order creation workflows, delivery processing, pricing calculation
- Property tests: Properties 6-8 (order validation, delivery availability, goods issue inventory)

**Production Module:**
- Unit tests: MRP calculation scenarios, order confirmation workflows
- Property tests: Properties 9-11 (MRP generation, availability checking, confirmation updates)

**Procurement Module:**
- Unit tests: PO creation, goods receipt, invoice verification
- Property tests: Properties 12-15 (requisition generation, three-way matching, FIFO, traceability)

**Cost Module:**
- Unit tests: Cost calculation examples, variance scenarios
- Property tests: Properties 16-18 (standard cost calculation, variance calculation, project accumulation)

**Platform Services:**
- Unit tests: Authentication flows, workflow execution, report generation
- Property tests: Property 19 (workflow approval progression)

### Integration Testing

**Module Integration Tests:**
- Sales → Production: Order-to-production flow
- Production → Procurement: MRP-to-purchase flow
- Procurement → Inventory: Receipt-to-stock flow
- Production → Cost: Order-to-cost flow

**End-to-End Scenarios:**
- Complete order-to-cash cycle
- Complete procure-to-pay cycle
- Complete plan-to-produce cycle

### Performance Testing

**Load Testing:**
- Concurrent user simulation (100, 500, 1000 users)
- Batch processing: 500 tasks within 60 seconds
- MRP calculation with 10,000+ materials

**Stress Testing:**
- Database connection pool exhaustion
- Memory usage under sustained load
- Recovery from resource constraints

### Security Testing

**Authentication & Authorization:**
- Role-based access control validation
- Three-role management verification
- Session management and timeout

**Data Security:**
- Encryption verification (transmission and storage)
- SQL injection prevention
- XSS and CSRF protection

### Compatibility Testing

**Domestic Environment Testing:**
- Test on all supported domestic databases
- Test on domestic operating systems
- Test with domestic CPUs and servers
- Verify no performance degradation

**Browser Compatibility:**
- Test on domestic browsers
- Responsive design validation
- Accessibility compliance

