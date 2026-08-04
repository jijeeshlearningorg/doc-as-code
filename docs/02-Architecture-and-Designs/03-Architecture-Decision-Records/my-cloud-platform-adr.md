# Architecture Decision Record: Multi-Tenant Cloud Platform API-First Service Delivery Architecture

* Status: **Proposed**
* Owner: Enterprise Architecture Team
* Deciders: Platform Architects, Security Lead, DevOps Lead, Product Management
* Working Group: Platform Engineering, Security Engineering, Automation Engineering
* Creation Date: 2024-02-08
* Last Revisited: 2024-02-08
* Revision: 1.0

---

## Context and Problem Statement

The `my-cloud-platform` (greenfield-code) is a comprehensive VMware-based cloud infrastructure platform integrating 14 distinct capabilities across compute, storage, networking, automation, security, and disaster recovery domains. The current codebase demonstrates a functional decomposition pattern with capability-specific modules (automation, backup, deployment, disaster recovery, security, and service brokering) but lacks a cohesive architectural decision framework for:

1. **API-First Service Delivery**: How should platform capabilities be exposed to consumers (internal teams, external tenants, automation systems)?
2. **Multi-Tenancy Isolation**: How should logical and operational separation be enforced across the 14 capabilities?
3. **Automation Orchestration**: How should provisioning, lifecycle management, and workflow execution be coordinated across heterogeneous VMware components?
4. **Security and Secrets Management**: How should encryption keys, credentials, and vault policies be managed across distributed platform services?
5. **Observability and Compliance**: How should monitoring, logging, and compliance automation be integrated into the platform delivery model?

The repository evidence shows 41 functions distributed across 5 core modules, indicating a need for architectural alignment on service boundaries, integration patterns, and operational governance.

---

## Assumptions

1. **Technology Stack Stability**: VMware Aria Suite (Automation, Orchestrator, Operations, Logs) and VMware Cloud Foundation (vSphere, vSAN, NSX-T, SDDC Manager) are the primary infrastructure platforms and will remain the strategic foundation for 3+ years.

2. **Multi-Tenancy Requirement**: The platform must support multiple independent tenants with strict logical and operational isolation, implying role-based access control (RBAC), namespace isolation, and tenant-specific billing/reporting.

3. **Automation-Driven Operations**: Infrastructure provisioning, lifecycle management, and disaster recovery operations are primarily driven by automated workflows rather than manual intervention, requiring robust orchestration and validation mechanisms.

4. **Secrets Management Centralization**: All encryption keys, credentials, and sensitive configuration must be managed through HashiCorp Vault with customer-managed key (CMK) support for compliance requirements.

5. **API Consumption Model**: Platform capabilities are consumed via REST APIs and service catalogs by internal automation systems, external cloud consumers, and third-party integrations.

6. **Compliance and Governance**: The platform must support automated compliance validation, audit logging, and reporting across all capabilities to meet enterprise security standards.

7. **Disaster Recovery Criticality**: Site protection, workload replication, and recovery capabilities are mission-critical with defined Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO).

8. **Observability Maturity**: Centralized monitoring (Aria Operations), logging (Aria Logs), and network analytics (Aria Network Insight) are operational prerequisites for platform health and troubleshooting.

---

## Constraints

1. **VMware Ecosystem Lock-In**: Strategic commitment to VMware technologies limits flexibility in compute, storage, and networking layer choices; alternative hypervisors or SDN platforms are not viable options.

2. **Kubernetes Integration Scope**: Tanzu Kubernetes Grid and Tanzu Mission Control provide container orchestration but must coexist with traditional VM-based workloads; dual-platform operational complexity is unavoidable.

3. **Backup and Recovery Complexity**: Multiple backup solutions (Canopy Enterprise Backup, Avamar, Data Domain) and recovery platforms (SRM, vSphere Replication, HCX) create operational overhead; consolidation is constrained by existing customer commitments.

4. **Multi-Cloud Integration Limitations**: VMware Cloud (VMC) integration with hyperscaler environments (AWS, Azure, GCP) is limited to specific regions and connectivity models; not all workloads can be migrated to public cloud.

5. **Regulatory and Compliance Requirements**: Customer-managed encryption keys, audit logging, and compliance automation are non-negotiable; this constrains architectural simplification.

6. **Operational Skill Requirements**: Platform operators must maintain expertise across 16+ VMware technologies; training and certification overhead is significant.

7. **Performance and Scalability Boundaries**: vSAN performance, NSX-T overlay network throughput, and Aria Automation workflow execution latency impose practical limits on platform scale and responsiveness.

8. **Backward Compatibility**: Existing customer deployments and integrations must be supported; breaking changes to APIs or service models are not acceptable.

---

## Considered Options

### Option 1: Monolithic Service Broker Architecture
**Description**: Consolidate all 14 capabilities into a single service broker API with capability-specific endpoints and unified tenant management.

**Pros**:
- Simplified API surface for consumers
- Centralized authentication and authorization
- Single point of governance and compliance
- Easier to implement cross-capability workflows

**Cons**:
- Single point of failure for all platform services
- Difficult to scale individual capabilities independently
- Tight coupling between capabilities increases change risk
- Operational complexity in troubleshooting cross-capability issues
- Violates microservices principles

**Concerns**:
- Does not align with modern cloud-native architecture patterns
- Difficult to implement independent capability versioning
- Tenant isolation becomes complex at the monolithic layer

---

### Option 2: Capability-Specific Microservices with API Gateway
**Description**: Decompose platform into 14 independent microservices (one per capability), each with its own API, data store, and operational lifecycle, fronted by a centralized API Gateway for routing, authentication, and rate limiting.

**Pros**:
- Independent scaling and deployment of capabilities
- Clear separation of concerns and team ownership
- Easier to implement capability-specific security policies
- Supports polyglot technology choices within capabilities
- Aligns with microservices and cloud-native patterns
- Enables independent versioning and lifecycle management

**Cons**:
- Increased operational complexity (14 services to manage)
- Distributed transaction management across capabilities
- Network latency and reliability concerns
- Requires sophisticated observability and tracing
- Higher infrastructure overhead

**Concerns**:
- Requires investment in container orchestration and service mesh
- Tenant isolation must be implemented at each microservice layer
- Cross-capability workflows require choreography or orchestration patterns

---

### Option 3: Hybrid Layered Architecture (Recommended)
**Description**: Organize platform into 4 logical layers:
- **Capability Layer**: 14 independent capability modules (compute, storage, networking, etc.) with internal APIs
- **Service Broker Layer**: Unified service catalog and API broker exposing capabilities as composable services
- **Orchestration Layer**: Aria Orchestrator-based workflow engine for multi-capability provisioning and lifecycle operations
- **Tenant Management Layer**: Multi-tenancy, RBAC, billing, and compliance enforcement

**Pros**:
- Balances modularity with operational simplicity
- Leverages existing Aria Orchestrator investment
- Clear separation between capability implementation and service delivery
- Supports both simple (single-capability) and complex (multi-capability) workflows
- Tenant isolation enforced at broker and orchestration layers
- Aligns with VMware architectural patterns

**Cons**:
- Requires careful API design between layers
- Orchestration layer becomes critical dependency
- Workflow complexity can grow rapidly
- Requires strong governance on capability API contracts

**Concerns**:
- Orchestration layer must handle failure scenarios gracefully
- Tenant isolation must be validated across all layers
- Cross-capability transaction semantics must be defined

---

### Option 4: Event-Driven Architecture with Message Bus
**Description**: Implement platform as event-driven system with capabilities publishing and subscribing to domain events (e.g., "VM Provisioned", "Storage Allocated", "Network Configured") via message broker (e.g., RabbitMQ, Kafka).

**Pros**:
- Loose coupling between capabilities
- Asynchronous processing enables better scalability
- Event sourcing provides audit trail and compliance benefits
- Supports complex multi-capability workflows naturally
- Enables real-time observability and analytics

**Cons**:
- Increased architectural complexity
- Eventual consistency model complicates error handling
- Requires investment in message broker infrastructure
- Debugging distributed event flows is challenging
- Not suitable for synchronous provisioning workflows

**Concerns**:
- Tenant isolation must be enforced at event level
- Event ordering and idempotency are critical
- Compliance and audit requirements may conflict with eventual consistency

---

## Proposed Design

### Architecture Overview

Implement **Option 3: Hybrid Layered Architecture** with the following structure:

```
┌─────────────────────────────────────────────────────────────┐
│                    Tenant Management Layer                   │
│  (Multi-Tenancy, RBAC, Billing, Compliance, Audit Logging)  │
└─────────────────────────────────────────────────────────────┘
                              ▲
                              │
┌─────────────────────────────────────────────────────────────┐
│                    Service Broker Layer                       │
│  (API Gateway, Service Catalog, API Registration, Validation)│
└─────────────────────────────────────────────────────────────┘
                              ▲
                              │
┌─────────────────────────────────────────────────────────────┐
│                  Orchestration Layer                          │
│  (Aria Orchestrator, Workflow Engine, State Management)      │
└─────────────────────────────────────────────────────────────┘
                              ▲
                              │
┌─────────────────────────────────────────────────────────────┐
│                    Capability Layer                           │
│  ┌──────────┬──────────┬──────────┬──────────┬──────────┐   │
│  │ Compute  │ Storage  │Networking│Automation│ Backup   │   │
│  │(vSphere) │(vSAN)    │(NSX-T)   │(Aria)    │(Canopy)  │   │
│  └──────────┴──────────┴──────────┴──────────┴──────────┘   │
│  ┌──────────┬──────────┬──────────┬──────────┬──────────┐   │
│  │Monitoring│ Security │    DR    │Containers│Lifecycle │   │
│  │(Aria Ops)│(Vault)   │(SRM)     │(Tanzu)   │(VLCM)    │   │
│  └──────────┴──────────┴──────────┴──────────┴──────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Layer Responsibilities

#### 1. Capability Layer
Each capability module exposes a well-defined internal API:

**Compute Capability** (`src/compute.py` - implied):
- `provision_vm(tenant_id, vm_spec)` → VM ID
- `configure_vm_resources(vm_id, cpu, memory, storage)`
- `get_vm_status(vm_id)` → Status object
- `delete_vm(vm_id)`

**Storage Capability** (`src/storage.py` - implied):
- `allocate_storage(tenant_id, size_gb, tier)` → Storage ID
- `configure_replication(storage_id, target_site)`
- `get_storage_metrics(storage_id)` → Metrics object

**Networking Capability** (`src/networking.py` - implied):
- `deploy_network_foundation(region)` → Network ID (from `src/deploy.py`)
- `create_network_segment(tenant_id, segment_spec)` → Segment ID
- `configure_routing(segment_id, routes)`
- `get_network_topology(tenant_id)` → Topology object

**Automation Capability** (`src/automation.py`):
- `provision_infrastructure(environment_name)` → Execution ID
- `execute_platform_workflow(workflow_name)` → Execution ID
- `deploy_configuration_baseline(environment_name)` → Status
- `validate_automation_results(workflow_name)` → Validation result

**Backup Capability** (`src/backup.py`):
- `schedule_backup_job(workload_name)` → Job ID
- `execute_backup(workload_name)` → Backup ID
- `validate_backup_integrity(backup_id)` → Integrity report
- `generate_backup_report()` → Report object

**Disaster Recovery Capability** (`src/dr_platform.py`):
- `create_recovery_plan(application_name)` → Plan ID
- `execute_site_failover(target_site)` → Failover status
- `validate_recovery_objectives(application_name)` → Validation result
- `generate_dr_readiness_report()` → Report object

**Security & Vault Capability** (`src/security_vault.py`):
- `create_vault_namespace(namespace_name)` → Namespace ID
- `create_customer_managed_key(key_name)` → Key ID
- `rotate_encryption_key(key_name)` → Rotation status
- `assign_key_to_service(key_name, service_name)` → Assignment status
- `validate_vault_policy(policy_name)` → Validation result

**Service Broker Capability** (`src/service_broker.py`):
- `publish_service_catalog(catalog_name)` → Catalog ID
- `register_platform_api(api_name)` → API ID
- `create_service_offering(service_name)` → Offering ID
- `validate_api_subscription(subscription_id)` → Validation result

**Deployment Capability** (`src/deploy.py`):
- `deploy_kubernetes_platform(cluster_name)` → Cluster ID
- `deploy_ai_platform(environment)` → Platform ID
- `deploy_data_platform(environment)` → Platform ID
- `validate_platform_observability(environment)` → Validation result

#### 2. Orchestration Layer
Aria Orchestrator-based workflow engine:

**Responsibilities**:
- Coordinate multi-capability provisioning workflows
- Manage state transitions and error handling
- Implement retry logic and compensation (rollback) patterns
- Enforce workflow policies and governance rules
- Provide workflow execution history and audit trail

**Key Workflows**:
- `provision_complete_environment`: Orchestrates compute + storage + networking + monitoring
- `failover_application`: Coordinates DR across compute, storage, and networking
- `backup_and_replicate`: Chains backup execution with replication configuration
- `rotate_encryption_keys`: Coordinates key rotation across all services

**Implementation**:
```yaml
Workflow: provision_complete_environment
  Input: tenant_id, environment_spec
  Steps:
    1. Validate tenant quota and permissions (Tenant Management Layer)
    2. Create vault namespace (Security Capability)
    3. Create customer-managed key (Security Capability)
    4. Deploy network foundation (Networking Capability)
    5. Allocate storage (Storage Capability)
    6. Provision VMs (Compute Capability)
    7. Configure monitoring (Monitoring Capability)
    8. Publish service catalog (Service Broker Capability)
    9. Generate audit log (Tenant Management Layer)
  Error Handling: Rollback all steps on failure
  Output: environment_id, status
```

#### 3. Service Broker Layer
Unified API gateway and service catalog:

**Responsibilities**:
- Expose capabilities as composable services via REST APIs
- Implement API versioning and backward compatibility
- Enforce rate limiting and quota management
- Validate API requests against tenant permissions
- Route requests to appropriate capability or orchestration layer
- Maintain service catalog and API registry

**API Structure**:
```
POST /api/v1/tenants/{tenant_id}/services/compute/vms
  → Calls Compute Capability or Orchestration Layer

POST /api/v1/tenants/{tenant_id}/services/storage/volumes
  → Calls Storage Capability

POST /api/v1/tenants/{tenant_id}/workflows/provision-environment
  → Calls Orchestration Layer

GET /api/v1/service-catalog
  → Returns available services and offerings

POST /api/v1/services/{service_id}/subscribe
