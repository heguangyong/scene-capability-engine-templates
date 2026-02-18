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

# Requirements Document: 国产化ERP系统POC测试

## Introduction

This document specifies the requirements for a domestically-produced ERP system POC (Proof of Concept) test. The system must support comprehensive manufacturing operations including production planning, procurement, inventory management, quality control, equipment management, and service support. The system must operate in a fully domestic IT environment (servers, CPUs, operating systems, databases, clients, plugins, and tools) with no performance degradation.

## Glossary

- **ERP_System**: The domestically-produced Enterprise Resource Planning system under evaluation
- **MPS**: Master Production Schedule - high-level production planning
- **MRP**: Material Requirements Planning - detailed material and production planning
- **BOM**: Bill of Materials - hierarchical structure of product components
- **ECN**: Engineering Change Notice - formal change management process
- **WBS**: Work Breakdown Structure - hierarchical project decomposition
- **POC**: Proof of Concept - evaluation test to verify system capabilities
- **Work_Center**: Production facility or resource where operations are performed
- **Routing**: Sequence of operations required to manufacture a product
- **Batch**: Group of materials tracked together with unique identifier
- **Serial_Number**: Unique identifier for individual items
- **Domestic_Environment**: IT infrastructure using only domestically-produced components

## Requirements

### Requirement 1: Master Data Management

**User Story:** As a data administrator, I want to manage master data for materials, BOMs, routings, and other core entities, so that the system has accurate foundational data for all business operations.

#### Acceptance Criteria

1. THE ERP_System SHALL support material master data creation, modification, and deletion with material codes exceeding 18 characters
2. THE ERP_System SHALL support multi-organizational material attributes including plant, storage location, and other dimensions
3. THE ERP_System SHALL maintain material attributes across multiple views including basic, inventory, purchasing, sales, financial, and production information
4. THE ERP_System SHALL support material status management including disabled, obsolete, and restricted states
5. THE ERP_System SHALL support MPS and MRP calculation parameters in material master data
6. THE ERP_System SHALL support custom data attribute validity configuration for non-calculation attributes
7. THE ERP_System SHALL support batch and serial number management for materials
8. THE ERP_System SHALL support BOM creation with multiple types including production, repair, and cost BOMs
9. THE ERP_System SHALL support multi-level BOM structures with tree hierarchy
10. THE ERP_System SHALL support co-products and by-products in BOM structures
11. THE ERP_System SHALL support material substitution in BOMs with priority and ratio settings
12. THE ERP_System SHALL support variant BOMs for configured products
13. THE ERP_System SHALL support routing creation with work centers, operations, and time standards
14. THE ERP_System SHALL support multiple routings per material for different production scenarios
15. THE ERP_System SHALL link work centers to cost centers for production cost allocation


### Requirement 2: Engineering Change Management

**User Story:** As an engineering manager, I want to manage engineering changes systematically, so that product modifications are tracked and executed correctly across all affected business processes.

#### Acceptance Criteria

1. WHEN an ECN is created, THE ERP_System SHALL calculate change impact across sales orders, purchase requests, production plans, work orders, and inventory
2. WHEN an ECN is executed, THE ERP_System SHALL record execution results and maintain change history
3. THE ERP_System SHALL support querying multiple change logs for the same BOM or routing
4. THE ERP_System SHALL support tracking changes by change number
5. THE ERP_System SHALL support time-effective and unit-effective change numbers

### Requirement 3: Sales Order Management

**User Story:** As a sales manager, I want to manage the complete sales order lifecycle, so that customer orders are processed accurately from creation through delivery and invoicing.

#### Acceptance Criteria

1. THE ERP_System SHALL support multiple sales order types including standard, free-of-charge, and return orders
2. THE ERP_System SHALL maintain complete sales order structure with header and line item information
3. THE ERP_System SHALL support sales order copy, modification, deletion, and closure operations
4. WHEN a sales order is created, THE ERP_System SHALL validate customer information, pricing, and delivery dates
5. WHEN a delivery is created, THE ERP_System SHALL verify sales order validity, inventory availability, and order type compatibility
6. THE ERP_System SHALL support picking operations with real-time status tracking
7. WHEN goods are issued, THE ERP_System SHALL reduce inventory and generate accounting documents
8. THE ERP_System SHALL support sales order approval workflows with configurable approval rules
9. WHEN a make-to-order sales order is created, THE ERP_System SHALL generate production requirements in planning module
10. WHEN a sales order requires purchased items, THE ERP_System SHALL automatically trigger procurement processes
11. THE ERP_System SHALL support sales order BOM configuration for each engine unit
12. THE ERP_System SHALL support cross-company transfer orders with automatic delivery and purchase order generation


### Requirement 4: Production Planning and Execution

**User Story:** As a production planner, I want to plan and execute production efficiently, so that manufacturing meets demand while optimizing resource utilization.

#### Acceptance Criteria

1. THE ERP_System SHALL support independent demand and reservation management with version control and activation status
2. THE ERP_System SHALL support multiple planning strategies including make-to-order (50), final assembly (40), make-to-stock with inventory (10), and make-to-stock without inventory (11)
3. WHEN demand date or quantity changes, THE ERP_System SHALL support plan rescheduling
4. THE ERP_System SHALL support MPS approval, release, and versioning within the system
5. THE ERP_System SHALL support repair and overhaul lifecycle from receipt through disassembly, inspection, repair, assembly, testing, and delivery
6. THE ERP_System SHALL support long-term simulation with configurable supply-demand models
7. THE ERP_System SHALL support work center grouping and capacity analysis considering equipment and actual work hours
8. THE ERP_System SHALL support single-level and multi-level MRP calculation with lot sizing, lead times, and priority constraints
9. THE ERP_System SHALL support demand consolidation based on period, batch size, and other configurable factors
10. THE ERP_System SHALL generate make, buy, and subcontract recommendations from MRP results
11. THE ERP_System SHALL support scrap rate configuration affecting material requirement calculations
12. THE ERP_System SHALL support materials with both make and buy options with configurable ratios
13. THE ERP_System SHALL support variant BOM MRP calculation and plan release
14. THE ERP_System SHALL provide exception messages and processing recommendations
15. THE ERP_System SHALL support production order creation, release, modification, freezing, and closure
16. THE ERP_System SHALL support production order status management with user-definable states
17. THE ERP_System SHALL support material availability checking with in-transit and on-hand modes
18. THE ERP_System SHALL support inventory reservation for production orders
19. THE ERP_System SHALL support material issue and return for production orders with batch and serial number management
20. THE ERP_System SHALL support co-product and by-product goods receipt
21. THE ERP_System SHALL support production reporting at operation and order completion levels
22. THE ERP_System SHALL support batch and non-batch production management modes
23. THE ERP_System SHALL support demand traceability from orders to source requirements
24. THE ERP_System SHALL support capacity planning and load balancing


### Requirement 5: Procurement and Inventory Management

**User Story:** As a procurement manager, I want to manage the complete procurement lifecycle and inventory operations, so that materials are available when needed at optimal cost.

#### Acceptance Criteria

1. THE ERP_System SHALL generate purchase requisitions automatically from MRP or manually
2. THE ERP_System SHALL support source lists and quota arrangements with supplier allocation
3. THE ERP_System SHALL generate purchase orders from requisitions using source lists and quotas
4. THE ERP_System SHALL support blanket purchase agreements with expiration warnings
5. THE ERP_System SHALL support multiple purchase order types including standard, return, consignment, and subcontracting
6. THE ERP_System SHALL support subcontracting with supplier inventory management
7. THE ERP_System SHALL support operation subcontracting for both fixed and temporary operations
8. WHEN goods are received, THE ERP_System SHALL create material documents and update inventory
9. WHEN goods are received, THE ERP_System SHALL generate financial documents if inventory accounting is enabled
10. THE ERP_System SHALL support goods receipt inspection with quality hold status
11. THE ERP_System SHALL support batch management with FIFO strategy
12. THE ERP_System SHALL support serial number management for individual item tracking
13. THE ERP_System SHALL support batch traceability upstream and downstream through production orders
14. THE ERP_System SHALL support various inventory movement types including transfers, issues, and receipts
15. THE ERP_System SHALL support invoice verification with three-way matching
16. THE ERP_System SHALL support supplier reconciliation with periodic cycles
17. THE ERP_System SHALL support purchase price management and price history


### Requirement 6: Cost Management

**User Story:** As a cost accountant, I want to track and analyze production costs accurately, so that management has reliable cost information for decision-making.

#### Acceptance Criteria

1. THE ERP_System SHALL calculate standard costs based on BOM, routing, material prices, and activity prices
2. THE ERP_System SHALL support standard cost updates and batch releases
3. THE ERP_System SHALL collect actual costs on production orders including material, labor, and overhead
4. THE ERP_System SHALL support cost element allocation and distribution rules
5. THE ERP_System SHALL support material ledger settlement to calculate actual material costs
6. THE ERP_System SHALL support variance analysis between standard and actual costs
7. THE ERP_System SHALL support cost traceability to lowest-level materials and cost elements
8. THE ERP_System SHALL support project cost management through internal orders
9. THE ERP_System SHALL support repair and overhaul cost collection and settlement
10. THE ERP_System SHALL support budget management based on MRP results

### Requirement 7: Project Management

**User Story:** As a project manager, I want to manage projects with WBS structures, so that project costs and progress are tracked systematically.

#### Acceptance Criteria

1. THE ERP_System SHALL support project creation with project number, description, and parameters
2. THE ERP_System SHALL support project status management including released, locked, closed, and deleted
3. THE ERP_System SHALL support WBS element creation with hierarchical structure
4. THE ERP_System SHALL support batch WBS creation from templates
5. THE ERP_System SHALL link production orders, purchase orders, and inventory to projects
6. THE ERP_System SHALL support R&D project budget management with cost collection and analysis


### Requirement 8: Equipment Management

**User Story:** As an equipment manager, I want to manage equipment lifecycle and maintenance, so that production equipment operates reliably.

#### Acceptance Criteria

1. THE ERP_System SHALL support equipment master data creation with unique identification
2. THE ERP_System SHALL support equipment status changes including transfer, capitalization, idle, disposal, and scrapping
3. THE ERP_System SHALL support equipment classification management
4. THE ERP_System SHALL support maintenance request creation and work order generation
5. THE ERP_System SHALL support preventive maintenance planning with schedules and strategies
6. THE ERP_System SHALL support maintenance work orders with routings and spare parts BOMs
7. THE ERP_System SHALL support equipment rental management for both incoming and outgoing rentals

### Requirement 9: Service and Support Management

**User Story:** As a service manager, I want to manage field service and repair operations, so that customer equipment is maintained effectively.

#### Acceptance Criteria

1. THE ERP_System SHALL support service notification creation for returned products with fault registration
2. THE ERP_System SHALL generate repair orders automatically from service notifications
3. THE ERP_System SHALL support repair order lifecycle from disassembly through completion
4. THE ERP_System SHALL support repair scrap collection to individual engine BOMs
5. THE ERP_System SHALL support repair cost collection including materials and labor
6. THE ERP_System SHALL support parts replacement rate analysis by customer and return type


### Requirement 10: Tool and Fixture Management

**User Story:** As a tool manager, I want to manage tools and fixtures throughout their lifecycle, so that production has necessary tooling available.

#### Acceptance Criteria

1. THE ERP_System SHALL support tool and fixture MRP calculation as production resources
2. THE ERP_System SHALL support tool purchase requisitions and orders linked to using departments
3. THE ERP_System SHALL support tool borrowing and return management
4. THE ERP_System SHALL support tool calibration planning with periodic schedules and expiration warnings
5. THE ERP_System SHALL support modular fixture configuration management
6. THE ERP_System SHALL support modular fixture set borrowing with individual component scrapping
7. THE ERP_System SHALL generate tool 2D barcodes for tracking
8. THE ERP_System SHALL support tool repair request management with status tracking

### Requirement 11: Data Compatibility and Migration

**User Story:** As a system administrator, I want to migrate data from existing systems, so that historical data is preserved during system transition.

#### Acceptance Criteria

1. THE ERP_System SHALL provide data migration functionality for customer-provided data
2. THE ERP_System SHALL provide cutover plan from SAP system with data completeness guarantees
3. THE ERP_System SHALL provide standard data migration tools, methodologies, and success cases
4. WHEN the system is upgraded, THE ERP_System SHALL automatically migrate data structures
5. THE ERP_System SHALL support custom field extensions in data models
6. THE ERP_System SHALL support data synchronization from production to test environments
7. THE ERP_System SHALL support data partitioning, read-write separation, and hybrid storage


### Requirement 12: Technical Architecture

**User Story:** As a system architect, I want an open and extensible architecture, so that the system can be customized and integrated with other systems.

#### Acceptance Criteria

1. THE ERP_System SHALL use open, extensible architecture including Java, HTML/CSS/JavaScript/Vue
2. THE ERP_System SHALL support modular development and multi-team collaboration
3. THE ERP_System SHALL support cluster deployment for applications and databases
4. THE ERP_System SHALL support blue-green deployment for production environments
5. THE ERP_System SHALL support microservices architecture with independent module deployment
6. THE ERP_System SHALL support private cloud and hybrid cloud deployment
7. THE ERP_System SHALL provide low-code development capabilities with visual design tools
8. THE ERP_System SHALL support high-code development using Java, Vue3, and JavaScript
9. THE ERP_System SHALL provide enhancement development mode similar to SAP with defined boundaries
10. THE ERP_System SHALL provide API integration module supporting RESTful and Web Service interfaces
11. THE ERP_System SHALL include built-in APIs covering major functional modules
12. THE ERP_System SHALL support access control, three-role management, security audit, and classification marking
13. THE ERP_System SHALL support data transmission and storage encryption
14. THE ERP_System SHALL support system collaboration between homogeneous systems


### Requirement 13: Performance and Scalability

**User Story:** As a system administrator, I want the system to handle high loads reliably, so that business operations are not disrupted during peak usage.

#### Acceptance Criteria

1. THE ERP_System SHALL support high availability and load balancing at application service layer
2. THE ERP_System SHALL separate user-facing operations from batch processing tasks
3. WHEN processing 500 batch tasks simultaneously, THE ERP_System SHALL complete within 60 seconds
4. THE ERP_System SHALL support hot standby for data storage with configurable backup cycles

### Requirement 14: User Experience and Usability

**User Story:** As a business user, I want an intuitive system with helpful features, so that I can work efficiently with minimal training.

#### Acceptance Criteria

1. THE ERP_System SHALL provide context-sensitive help functionality similar to SAP F1
2. THE ERP_System SHALL allow business consultants to configure parameters through UI similar to SAP SPRO
3. THE ERP_System SHALL provide operations management module with performance monitoring, alerts, and log analysis
4. THE ERP_System SHALL provide error code management with unique IDs and troubleshooting steps
5. THE ERP_System SHALL provide flexible report configuration similar to Excel with multiple filters
6. THE ERP_System SHALL support report export with classification markings
7. THE ERP_System SHALL support multi-level approval workflows with configurable approvers and conditions
8. THE ERP_System SHALL provide change log query for master data, demand, and order modifications
9. THE ERP_System SHALL provide AI-powered business process optimization with demonstration cases


### Requirement 15: Product Maturity and Stability

**User Story:** As a system administrator, I want seamless system upgrades, so that operations continue without disruption during version updates.

#### Acceptance Criteria

1. WHEN the system is upgraded, THE ERP_System SHALL provide seamless update experience
2. WHEN standard product is upgraded, THE ERP_System SHALL maintain interface compatibility
3. WHEN customized system is upgraded, THE ERP_System SHALL preserve custom functionality and data

### Requirement 16: Documentation and Support

**User Story:** As a system administrator, I want comprehensive documentation, so that I can understand, operate, and troubleshoot the system effectively.

#### Acceptance Criteria

1. THE ERP_System SHALL provide data dictionary by business module with table relationships
2. THE ERP_System SHALL provide system deployment records including paths and configurations
3. THE ERP_System SHALL provide three-role administrator operation manuals
4. THE ERP_System SHALL provide permission management manual with role configurations
5. THE ERP_System SHALL provide system configuration manual with parameter descriptions
6. THE ERP_System SHALL provide error code management with unique identifiers
7. THE ERP_System SHALL record error instances with user, computer, IP, error code, and context
8. THE ERP_System SHALL provide error troubleshooting procedures for each error code
9. THE ERP_System SHALL provide database-level query and modification tools with change logging
10. THE ERP_System SHALL provide business table query and modification with controlled scope
11. THE ERP_System SHALL provide reverse transaction functions for high-frequency operations
12. THE ERP_System SHALL provide audit report generation for classified systems
13. THE ERP_System SHALL provide audit functionality for security administrators
14. THE ERP_System SHALL provide interface call logging with structured parameter storage
15. THE ERP_System SHALL provide usage statistics for feature access tracking


### Requirement 17: Domestic Environment Compliance

**User Story:** As a compliance officer, I want the system to operate in fully domestic IT environment, so that we meet national security and localization requirements.

#### Acceptance Criteria

1. THE ERP_System SHALL operate on domestic servers with domestic CPUs
2. THE ERP_System SHALL operate on domestic operating systems
3. THE ERP_System SHALL operate with domestic databases
4. THE ERP_System SHALL operate with domestic client software
5. THE ERP_System SHALL operate with domestic plugins and tools
6. WHEN operating in domestic environment, THE ERP_System SHALL maintain full functionality without performance degradation
7. THE ERP_System SHALL support all domestic IT components listed in national localization catalog

### Requirement 18: Vendor Capabilities

**User Story:** As a procurement manager, I want to evaluate vendor capabilities, so that we select a reliable long-term partner.

#### Acceptance Criteria

1. THE Vendor SHALL provide company information including employee count, technical team size, and R&D investment ratio
2. THE Vendor SHALL provide local service teams in major cities including Shenyang, Changsha, Chengdu, Xi'an, Shanghai, and Guiyang
3. THE Vendor SHALL demonstrate rapid response capability for after-sales support

---

## Non-Functional Requirements

### Performance Requirements

- Batch processing: 500 concurrent tasks completed within 60 seconds
- System availability: Support high availability and load balancing
- Data backup: Support hot standby with configurable backup cycles

### Security Requirements

- Access control with three-role management (system admin, security admin, audit admin)
- Data encryption for transmission and storage
- Security audit logging for all sensitive operations
- Classification marking support for documents and reports

### Scalability Requirements

- Microservices architecture supporting independent module scaling
- Cluster deployment for applications and databases
- Support for future business growth without architectural changes

### Compatibility Requirements

- Full compatibility with domestic IT infrastructure
- Data migration from SAP and other existing systems
- Interface stability across version upgrades

### Usability Requirements

- Context-sensitive help system
- Configurable workflows and approval processes
- Flexible reporting with Excel-like capabilities
- AI-powered process optimization

