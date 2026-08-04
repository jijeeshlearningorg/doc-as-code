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
- Requires sophisticated service discovery and load balancing
- Tenant isolation must be implemented at each capability layer
- Increased monitoring and observability requirements

**Concerns**:
- Requires significant DevOps and SRE investment
- Distributed debugging and troubleshooting complexity
- Data consistency challenges across capability boundaries

---

### Option 3: Domain-Driven Design with Bounded Contexts
**Description**: Group 14 capabilities into 4-5 logical domains (Infrastructure, Security, Automation, Observability, Disaster Recovery), each with its own API, data model, and operational governance, with well-defined integration contracts between domains.

**Pros**:
- Balances microservices benefits with operational simplicity
- Aligns with business domains and team structures
- Enables independent capability evolution within domains
- Clearer integration contracts and dependencies
- Reduces operational overhead vs. 14 independent services
- Supports multi-tenancy at domain level

**Cons**:
- Requires careful domain boundary definition
- Cross-domain workflows still require orchestration
- Potential for domain boundary misalignment with organizational structure
- Intermediate complexity between monolithic and microservices

**Concerns**:
- Domain boundaries may need to evolve as platform matures
- Cross-domain consistency and transaction management

---

### Option 4: Hybrid Hub-and-Spoke Architecture
**Description**: Implement a central orchestration hub (Aria Orchestrator) that coordinates capability-specific services, with each capability exposing a standardized service interface (REST API, event streams) and the hub managing workflows, multi-tenancy, and cross-capability transactions.

**Pros**:
- Leverages existing Aria Orchestrator investment
- Centralized workflow orchestration and multi-tenancy
- Clear separation between orchestration and capability execution
- Supports complex multi-step provisioning workflows
- Enables event-driven architecture for asynchronous operations
- Aligns with VMware architectural patterns

**Cons**:
- Orchestrator becomes a critical bottleneck
- Requires standardization of capability interfaces
- Workflow complexity increases with platform scale
- Difficult to implement capability-specific optimizations
- Potential performance impact for high-throughput operations

**Concerns**:
- Orchestrator scalability and high availability requirements
- Workflow versioning and rollback complexity

---

## Proposed Design

### Architecture Overview

Implement a **Domain-Driven Design with Bounded Contexts** architecture, organized into 5 logical domains with a centralized API Gateway and event-driven integration layer:

```
┌─────────────────────────────────────────────────────────────┐
│                    API Gateway & Portal                      │
│         (Authentication, Authorization, Rate Limiting)       │
└────────────────────────┬────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼────────┐ ┌────▼──────────┐ ┌──▼──────────────┐
│ Infrastructure │ │   Security    │ │  Automation &   │
│    Domain      │ │    Domain     │ │  Orchestration  │
├────────────────┤ ├───────────────┤ ├─────────────────┤
│ • Compute      │ │ • Vault       │ │ • Provisioning  │
│ • Storage      │ │ • Encryption  │ │ • Workflows     │
│ • Networking   │ │ • Compliance  │ │ • Lifecycle Mgmt│
│ • Containers   │ │ • Secrets Mgmt│ │ • Validation    │
└────────┬────────┘ └───────┬───────┘ └────────┬────────┘
         │                  │                  │
         └──────────────────┼──────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
┌───────▼────────┐ ┌────────▼────────┐ ┌──────▼──────────┐
│ Observability  │ │ Disaster        │ │ Service Broker  │
│    Domain      │ │ Recovery Domain │ │    Domain       │
├────────────────┤ ├─────────────────┤ ├─────────────────┤
│ • Monitoring   │ │ • Site Recovery │ │ • Catalog       │
│ • Logging      │ │ • Replication   │ │ • API Registry  │
│ • Analytics    │ │ • Failover      │ │ • Subscriptions │
│ • Reporting    │ │ • Recovery Plans│ │ • Billing       │
└────────────────┘ └─────────────────┘ └─────────────────┘
         │                  │                   │
         └──────────────────┼───────────────────┘
                            │
        ┌───────────────────▼───────────────────┐
        │   Event Bus & Message Broker          │
        │  (Async Integration, Event Streaming) │
        └───────────────────────────────────────┘
```

### Domain Definitions

#### 1. Infrastructure Domain
**Capabilities**: Compute, Storage, Networking, Containers

**Services**:
- `compute-service`: VM provisioning, resource management, lifecycle
- `storage-service`: vSAN management, storage policies, capacity planning
- `networking-service`: NSX-T virtual networks, routing, segmentation
- `container-service`: Tanzu Kubernetes Grid deployment and management

**API Endpoints**:
```
POST   /api/v1/infrastructure/compute/vms
GET    /api/v1/infrastructure/compute/vms/{vm-id}
POST   /api/v1/infrastructure/storage/volumes
POST   /api/v1/infrastructure/networking/networks
POST   /api/v1/infrastructure/containers/clusters
```

**Data Model**: Tenant-scoped resources with RBAC enforcement

---

#### 2. Security Domain
**Capabilities**: Security, Secrets Management, Encryption, Compliance

**Services**:
- `vault-service`: HashiCorp Vault integration, namespace management
- `encryption-service`: Customer-managed key (CMK) lifecycle, key rotation
- `compliance-service`: Policy validation, audit logging, compliance reporting
- `secrets-service`: Credential management, secret rotation

**API Endpoints**:
```
POST   /api/v1/security/vault/namespaces
POST   /api/v1/security/encryption/keys
POST   /api/v1/security/encryption/keys/{key-id}/rotate
POST   /api/v1/security/compliance/policies/{policy-id}/validate
```

**Data Model**: Encrypted at rest, audit-logged, tenant-isolated

---

#### 3. Automation & Orchestration Domain
**Capabilities**: Automation, Provisioning, Lifecycle Management, Workflow Execution

**Services**:
- `provisioning-service`: Infrastructure provisioning orchestration
- `workflow-service`: Aria Orchestrator workflow execution and management
- `lifecycle-service`: Patching, upgrades, configuration management
- `validation-service`: Automation result validation, health checks

**API Endpoints**:
```
POST   /api/v1/automation/provisioning/environments
POST   /api/v1/automation/workflows/{workflow-id}/execute
POST   /api/v1/automation/lifecycle/patches
POST   /api/v1/automation/validation/results/{workflow-id}
```

**Data Model**: Workflow state, execution history, audit trail

---

#### 4. Observability Domain
**Capabilities**: Monitoring, Logging, Analytics, Reporting

**Services**:
- `monitoring-service`: Aria Operations metrics, alerts, dashboards
- `logging-service`: Aria Logs aggregation, search, analytics
- `analytics-service`: Network analytics (Aria Network Insight), trend analysis
- `reporting-service`: Operational, utilization, billing reports

**API Endpoints**:
```
GET    /api/v1/observability/monitoring/metrics/{resource-id}
GET    /api/v1/observability/logging/logs?query={query}
GET    /api/v1/observability/reporting/utilization?tenant={tenant-id}
```

**Data Model**: Time-series metrics, log events, aggregated analytics

---

#### 5. Disaster Recovery Domain
**Capabilities**: Disaster Recovery, Site Protection, Workload Replication, Recovery

**Services**:
- `recovery-planning-service`: Recovery plan creation and management
- `replication-service`: vSphere Replication, HCX workload mobility
- `failover-service`: Site failover orchestration and validation
- `readiness-service`: RTO/RPO validation, readiness reporting

**API Endpoints**:
```
POST   /api/v1/dr/recovery-plans
POST   /api/v1/dr/recovery-plans/{plan-id}/execute
GET    /api/v1/dr/readiness/objectives/{application-id}
```

**Data Model**: Recovery plans, replication state, failover history

---

#### 6. Service Broker Domain
**Capabilities**: API Service Broker, Service Catalog, Multi-Tenancy, Billing

**Services**:
- `catalog-service`: Service catalog management and publishing
- `api-registry-service`: Platform API registration and versioning
- `subscription-service`: API consumer subscriptions and entitlements
- `billing-service`: Usage tracking, metering, billing

**API Endpoints**:
```
GET    /api/v1/service-broker/catalog
POST   /api/v1/service-broker/apis/{api-id}/register
POST   /api/v1/service-broker/subscriptions
GET    /api/v1/service-broker/billing/usage?tenant={tenant-id}
```

**Data Model**: Catalog entries, API definitions, subscription records

---

### Multi-Tenancy Implementation

**Tenant Isolation Strategy**:
1. **Namespace Isolation**: Each tenant has dedicated Vault namespace, Kubernetes namespace, vSAN storage policy
2. **RBAC Enforcement**: Role-based access control at API Gateway and domain service levels
3. **Resource Quotas**: Compute, storage, network quotas per tenant
4. **Billing Isolation**: Usage metering and billing per tenant
5. **Audit Logging**: All operations logged with tenant context

**Tenant Context Propagation**:
```
Header: X-Tenant-ID: {tenant-id}
Header: X-User-ID: {user-id}
Header: X-Request-ID: {request-id}
```

---

### Integration Patterns

#### Synchronous Integration (Request-Response)
- Direct REST API calls between domains for immediate operations
- Example: Provisioning service calls Infrastructure domain to create VMs

#### Asynchronous Integration (Event-Driven)
- Event Bus (Apache Kafka, RabbitMQ) for decoupled communication
- Example: Infrastructure domain publishes "VM Created" event → Monitoring domain subscribes and creates monitoring policies

#### Orchestration Integration (Workflow-Driven)
- Aria Orchestrator coordinates multi-step workflows across domains
- Example: Provisioning workflow → Infrastructure → Security → Automation → Observability

---

### Security Architecture

**Authentication & Authorization**:
- API Gateway enforces OAuth 2.0 / OpenID Connect
- Service-to-service authentication via mTLS certificates
- Tenant context validated at each domain boundary

**Secrets Management**:
- All credentials stored in HashiCorp Vault
- Customer-managed encryption keys (CMK) for sensitive data
- Automatic key rotation policies
- Audit logging of all secret access

**Compliance & Audit**:
- Centralized audit logging to Aria Logs
- Compliance policy validation at provisioning time
- Automated compliance reporting

---

### Observability Architecture

**Monitoring**:
- Aria Operations collects metrics from all domains
- Custom dashboards per domain and per tenant
- Alert routing based on tenant and severity

**Logging**:
- Aria Logs aggregates logs from all services
- Structured logging with tenant context
- Log retention policies per compliance requirements

**Tracing**:
- Distributed tracing (OpenTelemetry) for cross-domain workflows
- Request correlation via X-Request-ID header

---

### Deployment Model

**Container-Based Microservices**:
- Each domain service deployed as containerized microservice
- Tanzu Kubernetes Grid for container orchestration
- Helm charts for service deployment and configuration

**Infrastructure Services**:
- VMware Aria Suite components deployed on vSphere
- SDDC Manager for lifecycle automation
- vSphere Lifecycle Manager (vLCM) for component updates

**Data Persistence**:
- PostgreSQL for transactional data (service broker, compliance)
- vSAN for persistent volumes (Kubernetes)
- HashiCorp Vault for secrets

---

## Decision Outcome

**ACCEPTED**: Implement a **Domain-Driven Design with Bounded Contexts** architecture for the `my-cloud-platform`, organized into 6 logical domains (Infrastructure, Security, Automation & Orchestration, Observability, Disaster Recovery, Service Broker) with the following key decisions:

### Primary Decisions

1. **Architecture Pattern**: Domain-Driven Design with Bounded Contexts
   - Rationale: Balances microservices scalability with operational simplicity; aligns with business domains and team structures; reduces operational overhead vs. 14 independent microservices

2. **API Gateway**: Centralized API Gateway for all external API consumption
   - Rationale: Single point of authentication, authorization, rate limiting, and tenant routing; simplifies API versioning and deprecation

3. **Multi-Tenancy Model**: Namespace-based isolation with RBAC enforcement
   - Rationale: Provides logical and operational separation; supports customer-managed encryption keys; enables per-tenant billing and compliance

4. **Integration Patterns**: Hybrid synchronous (REST) + asynchronous (Event Bus) + orchestration (Aria Orchestrator)
   - Rationale: Synchronous for immediate operations; asynchronous for decoupled domain communication; orchestration for complex multi-step workflows

5. **Secrets Management**: HashiCorp Vault with customer-managed keys (CMK)
   - Rationale: Centralized secrets management; supports compliance requirements; enables automatic key rotation

6. **Observability**: Centralized monitoring (Aria Operations), logging (Aria Logs), and distributed tracing
   - Rationale: Unified operational visibility; supports troubleshooting across domain boundaries; enables compliance audit trails

7. **Deployment Model**: Container-based microservices on Tanzu Kubernetes Grid + VMware Aria Suite components on vSphere
   - Rationale: Leverages existing VMware investments; supports independent scaling; enables modern DevOps practices

### Implementation Roadmap

**Phase 1 (Months 1-3)**: Foundation
- Implement API Gateway and authentication/authorization layer
- Deploy Service Broker domain (catalog, API registry, subscriptions)
- Establish event bus infrastructure

**Phase 2 (Months 4-6)**: Core Domains
- Implement Infrastructure domain (compute, storage, networking, containers)
- Implement Security domain (Vault integration, encryption, compliance)
- Establish multi-tenancy isolation

**Phase 3 (Months 7-9)**: Automation & Operations
- Implement Automation & Orchestration domain
- Implement Observability domain
- Establish distributed tracing and centralized logging

**Phase 4 (Months 10-12)**: Resilience & Maturity
- Implement Disaster Recovery domain
- Establish high availability and failover mechanisms
- Implement compliance automation and reporting

---

## Related Artifacts

### Architecture Documents
- VMware Cloud Foundation Reference Architecture
- VMware Aria Suite Deployment Guide
- Tanzu Kubernetes Grid Architecture Guide
- HashiCorp Vault Enterprise Architecture

### Design Documents
- API Gateway Design Specification
- Multi-Tenancy Isolation Design
- Event Bus Integration Patterns
- Security Architecture & Threat Model

### Related ADRs
- ADR-001: API Gateway Technology Selection (OpenAPI, Kong, AWS API Gateway)
- ADR-003: Event Bus Technology Selection (Kafka, RabbitMQ, AWS SQS)
- ADR-004: Container Orchestration Platform (Tanzu Kubernetes Grid vs. alternatives)
- ADR-005: Secrets Management Implementation (HashiCorp Vault vs. alternatives)

### Compliance & Governance
- Multi-Tenancy Security Policy
- Data Encryption Standards
- Audit Logging Requirements
- Compliance Automation Framework

### Operational Runbooks
- Domain Service Deployment Runbook
- Incident Response Procedures
- Disaster Recovery Procedures
- Capacity Planning Guidelines

---

## Consequences

### Positive Consequences

1. **Scalability**: Independent scaling of domains based on demand; Infrastructure domain can scale compute independently from Security domain
2. **Resilience**: Failure in one domain does not cascade to others; circuit breakers and bulkheads prevent cascading failures
3. **Agility**: Teams can develop and deploy domain services independently; faster time-to-market for new capabilities
4. **Maintainability**: Clear separation of concerns; easier to understand, test, and debug individual domains
5. **Multi-Tenancy**: Robust tenant isolation with per-tenant billing and compliance
6. **Compliance**: Centralized audit logging and compliance automation; easier to meet regulatory requirements
7. **Observability**: Unified monitoring and logging across all domains; easier to troubleshoot issues
8. **Flexibility**: Domains can evolve independently; easier to adopt new technologies within domain boundaries

### Negative Consequences

1. **Operational Complexity**: 6 domains + API Gateway + Event Bus = increased operational overhead; requires sophisticated DevOps and SRE practices
2. **Network Latency**: Synchronous inter-domain calls introduce network latency; asynchronous patterns mitigate but add complexity
3. **Data Consistency**: Distributed transactions across domains are complex; eventual consistency model required
4. **Debugging Difficulty**: Troubleshooting issues that span multiple domains requires distributed tracing and correlation
5. **Deployment Complexity**: 6+ independent services to deploy, version, and manage; requires CI/CD automation
6. **Testing Complexity**: Integration testing across domains is complex; requires comprehensive test automation
7. **Skill Requirements**: Teams must understand domain-driven design, microservices patterns, and distributed systems
8. **Cost**: Additional infrastructure for API Gateway, Event Bus, monitoring, and logging; increased cloud/infrastructure costs

### Mitigation Strategies

1. **Operational Complexity**: Invest in infrastructure-as-code (Terraform, Ansible), CI/CD automation (GitLab CI, Jenkins), and observability tooling
2. **Network Latency**: Implement caching, connection pooling, and asynchronous patterns; monitor and optimize latency
3. **Data Consistency**: Implement saga pattern for distributed transactions; use event sourcing for audit trails
4. **Debugging Difficulty**: Implement distributed tracing (OpenTelemetry), centralized logging (Aria Logs), and correlation IDs
5. **Deployment Complexity**: Automate deployment via Helm charts, GitOps (ArgoCD), and infrastructure-as-code
6. **Testing Complexity**: Implement contract testing, integration testing, and chaos engineering practices
7. **Skill Requirements**: Invest in team training, documentation, and knowledge sharing; hire experienced architects
8. **Cost**: Optimize resource utilization, implement auto-scaling, and monitor cloud costs

---

## Assumptions Validation

| Assumption | Validation Method | Validation Frequency |
|-----------|-------------------|----------------------|
| VMware Aria Suite stability | Quarterly review of VMware roadmap and release notes | Quarterly |
| Multi-tenancy requirement | Customer interviews and requirements analysis | Annually |
| Automation-driven operations | Operational metrics and workflow execution analysis | Monthly |
| Secrets management centralization | Security audit and compliance assessment | Semi-annually |
| API consumption model | API usage analytics and consumer feedback | Monthly |
| Compliance and governance | Compliance audit and regulatory assessment | Annually |
| Disaster recovery criticality | RTO/RPO validation and recovery testing | Quarterly |
| Observability maturity | Monitoring and logging coverage assessment | Monthly |

---

## Comments

### Architecture Review Board
- **Approved by**: Enterprise Architecture Review Board
- **Date**: 2024-02-08
- **Feedback**: Architecture aligns with enterprise cloud strategy; recommend proceeding with Phase 1 implementation

### Security Review
- **Reviewed by**: Chief Information Security Officer (CISO)
- **Date**: 2024-02-08
- **Feedback**: Multi-tenancy isolation and encryption key management meet compliance requirements; recommend security architecture review before Phase 2

### Operations Review
- **Reviewed by**: VP of Infrastructure Operations
- **Date**: 2024-02-08
- **Feedback**: Operational complexity is significant; recommend investing in automation and observability tooling; support Phase 1 implementation with resource allocation

### Product Management
- **Reviewed by**: Senior Product Manager
- **Date**: 2024-02-08
- **Feedback**: Architecture supports planned product roadmap; recommend API-first approach for third-party integrations; support Phase 1 implementation

---

## Revision History

| Revision | Date | Author | Changes |
|----------|------|--------|---------|
| 1.0 | 2024-02-08 | Enterprise Architecture Team | Initial ADR creation; Domain-Driven Design architecture proposed |

---

**Document Classification**: Internal Use Only  
**Next Review Date**: 2024-05-08 (Post Phase 1 Implementation)
