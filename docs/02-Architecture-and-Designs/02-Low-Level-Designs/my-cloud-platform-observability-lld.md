# Low-Level Design (LLD): my-cloud-platform

**Author:** Lead Solution Architect
**Date:** Auto-generated from repository analysis
**Version:** 1.0
**Status:** Draft
**Owner:** Platform Engineering / Cloud Architecture Team

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Solution Architect | TBD | Pending | TBD |
| Security Architect | TBD | Pending | TBD |
| Platform Owner | TBD | Pending | TBD |
| Service Owner | TBD | Pending | TBD |
| Operations Representative | TBD | Pending | TBD |

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Automated LLD Generator | Documentation Pipeline | Auto-generated | Generated from `jijeeshlearningorg/greenfield-code` (branch: `main`) |

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | Auto-generated | Initial LLD derived from repository scan (8 files, 41 functions, 0 classes, 4 imports) | Lead Solution Architect |

---

# 2. Related Documents

| Document Type | Document Reference | Link | Relationship |
|----------|----------|----------|----------|
| HLD | my-cloud-platform HLD | Internal Repository | Parent Design |
| LLD | my-cloud-platform LLD (this document) | `jijeeshlearningorg/greenfield-code` | Current Document |
| BIG | Build & Installation Guide (TBD) | N/A | Build Guide |
| OPG | Operations Guide (TBD) | N/A | Operations Guide |
| ADR | Architecture Decision Records (TBD) | N/A | Design Decisions |
| Vendor Documentation | VMware vSphere, NSX-T, Aria Suite, SDDC Manager, HashiCorp Vault, Canopy Enterprise Backup docs | External | Reference |

---

# 3. HLD Traceability Matrix

| HLD Requirement | HLD Section | LLD Section | Implementation Approach |
|----------|----------|----------|----------|
| Automated infrastructure provisioning | HLD - Automation Architecture | 7.5, 13 | `src/automation.py` (`provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`) |
| Backup & recovery services | HLD - Data Protection | 7.5, 11.3 | `src/backup.py` (`schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`) |
| Core platform deployment (network, Kubernetes, AI, data) | HLD - Deployment Architecture | 6, 7, 16 | `src/deploy.py` (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`) |
| Disaster recovery & site resilience | HLD - Resilience Architecture | 11.2, 15.6 | `src/dr_platform.py` (`create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`) |
| Secrets & encryption key management | HLD - Security Architecture | 10.5, 10.6 | `src/security_vault.py` (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`) |
| Service catalog & API consumption layer | HLD - Service Broker Architecture | 9, 7.5 | `src/service_broker.py` (`publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`) |
| CI/CD change impact detection & documentation automation | HLD - Automation Pipeline | 13 | `scripts/detect-impact.py` (impact detection, capability mapping, PR metadata extraction) |

---

# 4. Design Inputs

## 4.1 Design References

- Parent HLD for `my-cloud-platform`
- VMware Cloud Foundation (SDDC Manager) reference architecture
- VMware Validated Solutions for vSphere, NSX-T, vSAN, Aria Suite
- HashiCorp Vault enterprise deployment standards
- Canopy Enterprise Backup / Avamar / Data Domain integration guides
- Internal security and compliance policies

## 4.2 Technical Constraints

- Platform built on VMware SDDC stack (vSphere, vSAN, NSX-T) as the underlying compute/storage/network fabric.
- Automation is implemented in Python (`src/*.py`) and orchestrated via VMware Aria Automation / Aria Orchestrator workflows.
- CI/CD pipeline (`scripts/detect-impact.py`) requires JSON/YAML input for changed-file detection; no external package dependencies beyond 4 imports detected (standard library assumed: `json`, `os`, `re`, `sys`).
- No object-oriented class hierarchy exists in the current codebase (0 classes detected) — all logic is implemented as discrete, stateless functions.
- Backup and DR functions currently return boolean/dict status objects; no persistent state store detected in repository.

## 4.3 Design Drivers

- High availability and resilience across compute, storage, and networking layers.
- Repeatable, auditable automation for provisioning and lifecycle operations.
- Strong secrets/key management via enterprise vault integration.
- Multi-tenant isolation and service consumption through the API/service broker layer.
- Continuous validation of automation outcomes and observability posture.
- Traceability of infrastructure changes to impacted product capabilities via automated impact detection.

---

# 5. Implementation Decisions & Alternatives

| Decision | Alternatives Considered | Rationale |
|----------|----------|----------|
| Implement platform operations as discrete Python functions rather than class-based modules | Object-oriented service classes with encapsulated state | Current repository evidence shows a functional, stateless design (0 classes, 41 functions) suited to workflow-style automation invoked by orchestrators (Aria Orchestrator, CI/CD) |
| Use `scripts/detect-impact.py` as a standalone impact-detection utility rather than embedding logic in CI YAML | Embedding capability-mapping logic directly in pipeline YAML | Centralizing logic in Python enables regex/YAML fallback parsing, unit testability, and reuse across pipelines |
| Separate concerns by domain file (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`) | Monolithic single automation module | Improves maintainability and aligns each module to a distinct product capability (automation, backup, deployment, DR, security, API/service broker) |
| Boolean/dict return contracts for all automation functions | Structured exception-based error handling | Enables simple success/failure gating in CI/CD and orchestration pipelines without complex exception propagation |
| Path-based capability mapping (`resolve_capabilities_for_changed_file`) for documentation refresh triggers | Manual documentation update process | Enables automated, deterministic mapping of code changes to impacted product capabilities for LLD/HLD refresh triggers |

---

# 6. Detailed Architecture

## 6.1 Logical Design

The platform is composed of six functional domains implemented as independent Python modules under `src/`, plus one automation/CI utility under `scripts/`:

- **Automation Domain** (`src/automation.py`) — orchestrates infrastructure provisioning, workflow execution, configuration baselines, and validation.
- **Backup Domain** (`src/backup.py`) — manages backup scheduling, execution, integrity validation, and reporting.
- **Deployment Domain** (`src/deploy.py`) — deploys network foundation, Kubernetes platform, AI platform, and data platform services, plus observability validation.
- **Disaster Recovery Domain** (`src/dr_platform.py`) — manages recovery plans, site failover, recovery objective validation, and DR readiness reporting.
- **Security/Vault Domain** (`src/security_vault.py`) — manages vault namespaces, customer-managed keys, key rotation, service key assignment, and policy validation.
- **Service Broker Domain** (`src/service_broker.py`) — publishes service catalogs, registers platform APIs, creates service offerings, and validates API subscriptions.
- **CI/CD Impact Detection** (`scripts/detect-impact.py`) — detects changed files in a pull request, maps them to product capabilities, and emits a documentation refresh request payload (JSON).

Each domain module exposes a set of stateless functions consumed by an upstream orchestrator (Aria Automation / Aria Orchestrator workflows, or a CI/CD pipeline), following a call → validate → report pattern.

## 6.2 Physical Design

### On-Premises

- **Datacenter:** VMware Cloud Foundation-based SDDC, managed via SDDC Manager.
- **Cluster:** vSphere clusters providing compute (ESXi hosts) with vSAN for software-defined storage.
- **Rack:** Standard SDDC rack unit per VCF Validated Design (management, workload, and edge domains).
- **Host Placement:** ESXi hosts distributed across management domain (vCenter, NSX-T Manager, Aria Suite) and workload domains (tenant workloads, Tanzu Kubernetes Grid).

### Cloud

- **Public Cloud Integration:** VMware Cloud (VMC) based integration for hybrid extension, using HCX for workload mobility.
- **Region/Account Structure:** Aligned with hyperscaler account/subscription boundaries per tenant workload placement policy.

### Kubernetes / OpenShift

- **Cluster:** Tanzu Kubernetes Grid (TKG) clusters provisioned via `deploy_kubernetes_platform`.
- **Namespace Structure:** Per-tenant/per-workload namespace segmentation aligned to multi-tenancy capability.
- **Node Pools:** Separate node pools for control plane, general workloads, and AI/data platform workloads (`deploy_ai_platform`, `deploy_data_platform`).
- **Network Policies:** Enforced via NSX-T integration with TKG CNI.

---

# 7. Component Design

## 7.1 Compute / Runtime Design

- **Virtual Machines:** vSphere-managed VMs form the base compute layer for management and workload domains.
- **Containers:** Tanzu Kubernetes Grid clusters provisioned via `deploy_kubernetes_platform(cluster_name)`.
- **Serverless/Function Runtime:** Automation functions in `src/*.py` are designed to be invoked as discrete units of work (function-per-capability) by an orchestration engine (Aria Orchestrator) or CI/CD runner — resembling a serverless/workflow execution model.
- **Scaling Model:** Cluster and node-pool scaling managed through Aria Automation blueprints; AI and data platform workloads scaled independently via `deploy_ai_platform` / `deploy_data_platform`.

## 7.2 Storage Design

- **Storage Type:** VMware vSAN (primary, software-defined) with optional Fibre Channel-based storage for specific workloads.
- **Data Layout:** vSAN datastores per cluster; backup data landed on Data Domain / Avamar targets via `execute_backup`.
- **Capacity Planning:** Governed by cluster sizing standards (see Section 17).
- **Replication Strategy:** VM-level replication via vSphere Replication / SRM for DR (`create_recovery_plan`, `execute_site_failover`).

## 7.3 Network Design

### Logical Network

NSX-T provides logical segmentation, routing, and micro-segmentation across management, workload, and edge domains. `deploy_network_foundation(region)` establishes the core networking components (segments, gateways, edge clusters) for a new region/platform instance.

### Physical Network

Underlay leaf-spine fabric supporting NSX-T overlay networking, consistent with VMware Validated Design guidance.

### Connectivity Paths

- North-South: NSX-T Edge Gateways to physical uplinks.
- East-West: NSX-T distributed routing/micro-segmentation between workloads.
- Hybrid: HCX-based connectivity between on-premises SDDC and VMC/public cloud.

### Network Security Zones

- Management Zone (vCenter, NSX-T Manager, Aria Suite, SDDC Manager)
- Workload Zone (tenant VMs, TKG clusters)
- Edge/DMZ Zone (NSX-T Edge, external connectivity)
- DR Zone (replicated workloads at recovery site)

## 7.4 Platform Configuration

- **Hypervisor Configuration:** ESXi host configuration and lifecycle managed via vSphere Lifecycle Manager (vLCM) and SDDC Manager.
- **Middleware Configuration:** Aria Automation/Orchestrator workflows configured to invoke `src/automation.py` functions for baseline configuration (`deploy_configuration_baseline`).
- **OS Configuration:** Guest OS baseline configuration applied as part of configuration baseline automation.
- **Cluster Configuration:** vSphere/vSAN/NSX-T cluster configuration validated post-deployment via `validate_automation_results` and `validate_platform_observability`.

## 7.5 Application / Service Components

| Component | Purpose | Dependencies |
|----------|----------|----------|
| `src/automation.py` — Automation Engine | Provisions infrastructure, executes platform workflows, applies configuration baselines, validates automation outcomes | Aria Automation, Aria Orchestrator, target environment inventory |
| `src/backup.py` — Backup Service | Schedules and executes workload backups, validates backup integrity, generates backup reports | Canopy Enterprise Backup, Avamar, Data Domain |
| `src/deploy.py` — Deployment Engine | Deploys network foundation, Kubernetes platform, AI platform, data platform; validates observability | NSX-T, Tanzu Kubernetes Grid, Aria Operations, Aria Logs |
| `src/dr_platform.py` — DR Orchestrator | Creates recovery plans, executes site failover, validates recovery objectives, generates DR readiness reports | Site Recovery Manager (SRM), vSphere Replication |
| `src/security_vault.py` — Vault/Key Management Service | Creates vault namespaces, customer-managed keys, rotates keys, assigns keys to services, validates vault policy | HashiCorp Vault |
| `src/service_broker.py` — Service Broker | Publishes service catalog, registers platform APIs, creates service offerings, validates API subscriptions | Service Broker / API Gateway platform |
| `scripts/detect-impact.py` — Impact Detection Utility | Reads changed files, maps to capabilities, generates documentation refresh request | CI/CD pipeline, YAML capability mapping configuration |

---

# 8. Data Design

## 8.1 Data Flow

1. CI/CD pipeline triggers `scripts/detect-impact.py`, which reads changed files (`read_changed_files`) and a capability/path mapping (`read_yaml`), normalizes paths (`normalize_path`), resolves impacted capabilities (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`), and writes a documentation request payload (`write_json`) consumed downstream by the documentation generation pipeline.
2. Deployment functions in `src/deploy.py` execute sequentially — network foundation, then Kubernetes/AI/data platform deployment — followed by observability validation.
3. Automation functions in `src/automation.py` provision infrastructure, execute workflows, apply baselines, and validate outcomes, returning boolean success/failure signals to the orchestrator.
4. Backup functions capture workload state (`schedule_backup_job`, `execute_backup`), validate integrity (`validate_backup_integrity`), and produce reporting data (`generate_backup_report`).
5. DR functions define and validate recovery plans and produce readiness reporting (`generate_dr_readiness_report`).
6. Security/vault functions manage the lifecycle of namespaces and encryption keys, associating keys with consuming services.
7. Service broker functions publish catalog entries and APIs, and validate consumer subscriptions.

## 8.2 Data Storage

- Pipeline metadata (repository name, PR number, PR title, PR URL) extracted via `get_repository_name`, `get_repository_full_name`, `get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url` and persisted as JSON via `write_json`.
- Backup data persisted to Data Domain storage appliances via Avamar/Canopy Enterprise Backup.
- Vault-managed secrets and keys persisted within HashiCorp Vault's internal secure storage backend.
- DR readiness and backup reports returned as in-memory `dict` objects (`generate_backup_report`, `generate_dr_readiness_report`) for downstream consumption/reporting.

## 8.3 Database Objects

Not applicable — no relational/NoSQL schema objects detected in repository. Data structures are transient (in-memory dicts/lists) or externalized to platform tools (Vault, Data Domain, Aria Operations).

## 8.4 Data Access Design

- **APIs:** Platform functionality exposed via `service_broker.py` (`register_platform_api`, `create_service_offering`) as the primary API/service consumption layer.
- **ORM:** Not applicable (no database layer detected).
- **Queries:** Configuration and capability mappings read via `read_yaml`/regex-based parsing rather than structured queries.
- **Data Access Patterns:** Function-based, single-purpose calls returning boolean/dict results; no persistent connection/session objects detected.

## 8.5 Data Classification

| Data Type | Classification |
|----------|----------|
| Encryption keys (customer-managed) | Highly Confidential |
| Vault namespace/policy data | Confidential |
| Backup data (workload images/application data) | Confidential |
| DR recovery plans / readiness reports | Internal |
| Automation workflow execution results | Internal |
| CI/CD change-impact metadata (PR title, URL, repo name) | Internal |
| Service catalog / API registration metadata | Internal |

---

# 9. Integration Design

## 9.1 External Systems

| System | Purpose | Integration Type |
|----------|----------|----------|
| VMware vCenter / vSphere | Compute virtualization management | API/Workflow (Aria Automation) |
| NSX-T Manager | Network segmentation and routing | API/Workflow |
| VMware Aria Automation / Orchestrator | Workflow execution and orchestration | Workflow invocation of `src/automation.py`, `src/deploy.py` |
| VMware Aria Operations / Aria Logs | Observability, telemetry, log aggregation | Validated via `validate_platform_observability` |
| Tanzu Kubernetes Grid | Kubernetes runtime provisioning | API/CLI invoked via `deploy_kubernetes_platform` |
| HashiCorp Vault | Secrets and encryption key management | API integration via `src/security_vault.py` |
| Canopy Enterprise Backup / Avamar / Data Domain | Backup execution and storage | API/Agent integration via `src/backup.py` |
| VMware Site Recovery Manager (SRM) / vSphere Replication | Disaster recovery orchestration | API/Workflow via `src/dr_platform.py` |
| Service Broker / API Gateway | Service catalog and API exposure | API integration via `src/service_broker.py` |
| CI/CD Platform (GitHub Actions inferred) | Change detection and documentation automation | Script invocation of `scripts/detect-impact.py` |

## 9.2 Interfaces & APIs

| Interface | Protocol | Authentication |
|----------|----------|----------|
| Aria Automation Workflow API | HTTPS/REST | Token/SSO (vIDM) |
| NSX-T Manager API | HTTPS/REST | Token/RBAC |
| HashiCorp Vault API | HTTPS/REST | Vault Token / AppRole |
| Backup Platform API (Canopy/Avamar) | HTTPS/REST | Service Account Credentials |
| SRM/vSphere Replication API | HTTPS/REST | vCenter SSO |
| Service Broker/API Gateway | HTTPS/REST | API Key / OAuth2 |
| CI/CD Pipeline Environment Variables | Process Environment | Pipeline Secrets (GitHub Actions Secrets inferred) |

## 9.3 Message Flows

Not applicable — repository evidence indicates synchronous, function-call-based invocation rather than asynchronous message queuing.

---

# 10. Security Design

## 10.1 Identity & Access Management

Access to vSphere, NSX-T, Aria Suite, and Vault is managed through centralized SSO (vCenter SSO / vIDM) with role-based access enforced at the platform tool level. `create_vault_namespace` establishes namespace-level isolation boundaries for tenant/service secrets.

## 10.2 RBAC Model

RBAC enforced at:
- vSphere/vCenter (cluster/host/VM-level roles)
- NSX-T (network administration roles)
- HashiCorp Vault (namespace and policy-based access via `validate_vault_policy`)
- Service Broker (subscription-based consumer access via `validate_api_subscription`)

## 10.3 Service Accounts

Automation and orchestration functions (`src/automation.py`, `src/deploy.py`) are assumed to execute under dedicated service accounts scoped to Aria Automation/Orchestrator, with least-privilege access to vCenter, NSX-T, and Kubernetes APIs.

## 10.4 Network Security

NSX-T micro-segmentation enforces zone isolation (Management, Workload, Edge/DMZ, DR) as defined in Section 7.3. Trend Micro provides endpoint/anti-malware protection; Nessus provides vulnerability scanning across the platform.

## 10.5 Encryption

### Encryption At Rest

Customer-managed encryption keys created via `create_customer_managed_key(key_name)` and assigned to services via `assign_key_to_service(key_name, service_name)`, supporting encryption of vSAN datastores and backup repositories.

### Encryption In Transit

TLS enforced across all API integrations (vCenter, NSX-T, Vault, Backup, SRM, Service Broker) per platform standard (inferred).

## 10.6 Secrets Management

### Vault Integration

HashiCorp Vault integration implemented in `src/security_vault.py`:
- `create_vault_namespace(namespace_name)` — establishes isolated secrets namespace.
- `validate_vault_policy(policy_name)` — validates policy assignment compliance.

### Key Management

- `create_customer_managed_key(key_name)` — creates new encryption key.
- `rotate_encryption_key(key_name)` — rotates existing key per key-rotation policy.
- `assign_key_to_service(key_name, service_name)` — binds key to consuming service.

### Certificate Management

Not explicitly implemented in repository; assumed managed via platform PKI (vCenter/NSX-T certificate services) — flagged as an open dependency (see Section 12.5).

## 10.7 System Hardening

ESXi/vCenter hardening aligned to VMware security configuration guides; Trend Micro endpoint protection and Nessus vulnerability scanning applied across compute and management layers.

## 10.8 Security Logging

### Audit Logging

Automation outcome validation (`validate_automation_results`) and vault policy validation (`validate_vault_policy`) provide auditable pass/fail signals suitable for audit log capture.

### Security Event Logging

Aria Logs aggregates security-relevant events from ESXi, vCenter, NSX-T, and Vault.

### SIEM Integration

Not explicitly present in repository; Aria Logs is the inferred aggregation point for potential SIEM forwarding.

---

# 11. Availability & Resilience

## 11.1 High Availability Design

vSphere HA/DRS clusters provide compute-level resilience; NSX-T Edge clusters deployed in HA pairs; Aria Suite components deployed per validated HA design.

## 11.2 Disaster Recovery Design

Implemented via `src/dr_platform.py`:
- `create_recovery_plan(application_name)` — defines per-application recovery plan.
- `execute_site_failover(target_site)` — executes failover to target DR site.
- `validate_recovery_objectives(application_name)` — validates RPO/RTO compliance.
- `generate_dr_readiness_report()` — produces DR readiness status reporting.

Underlying replication provided by SRM and vSphere Replication.

## 11.3 Backup Design

Implemented via `src/backup.py`:
- `schedule_backup_job(workload_name)` — schedules backup jobs per workload.
- `execute_backup(workload_name)` — triggers backup execution.
- `validate_backup_integrity(backup_id)` — validates completed backup integrity.
- `generate_backup_report()` — produces backup status reporting.

Backend platforms: Canopy Enterprise Backup, Avamar, Data Domain.

## 11.4 Failover Design

Site-level failover orchestrated through `execute_site_failover(target_site)`, with pre-failover validation via `validate_recovery_objectives` and post-failover readiness confirmed via `generate_dr_readiness_report`.

---

# 12. Dependencies & Prerequisites

## 12.1 Infrastructure Dependencies

- vSphere clusters (ESXi, vCenter) provisioned and operational.
- vSAN datastores configured and healthy.
- NSX-T Manager and Edge clusters deployed.
- SDDC Manager managing lifecycle of the VCF instance.

## 12.2 Software Dependencies

- VMware Aria Automation / Aria Orchestrator (workflow execution).
- VMware Aria Operations / Aria Logs (observability).
- Tanzu Kubernetes Grid (container runtime).
- HashiCorp Vault (secrets/key management).
- Canopy Enterprise Backup, Avamar, Data Domain (backup).
- VMware SRM, vSphere Replication (DR).
- Python runtime for `src/*.py` and `scripts/detect-impact.py` execution.

## 12.3 External Dependencies

- Hyperscaler connectivity (VMC/public cloud integration) for hybrid workload mobility (HCX).
- CI/CD platform (GitHub Actions inferred) for pipeline execution of `scripts/detect-impact.py`.

## 12.4 Access Dependencies

- Service account credentials for vCenter, NSX-T, Aria Suite, Vault, Backup, and SRM API access.
- CI/CD pipeline secrets for repository/PR metadata retrieval (`get_repository_name`, `get_pull_request_number`, etc.).

## 12.5 Security Dependencies

### Secrets

Vault namespaces and keys must be provisioned prior to service key assignment (`assign_key_to_service`).

### Certificates

Platform PKI/certificate issuance process required for TLS across all integrated systems (not explicitly implemented in repository — dependency flagged).

### PKI

vCenter/NSX-T internal CA or enterprise PKI integration assumed as prerequisite.

### IAM

Centralized SSO (vIDM) integration required for all platform tool authentication.

---

# 13. Automation & Configuration Design

## 13.1 Automation Tools

- **Python** — core implementation language for all automation modules (`src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`, `scripts/detect-impact.py`).
- **VMware Aria Automation / Aria Orchestrator** — workflow orchestration layer invoking Python automation functions.
- **CI/CD Pipeline (GitHub Actions inferred)** — executes `scripts/detect-impact.py` on pull requests to detect changed files and trigger documentation refresh.

## 13.2 Repository Structure

```
greenfield-code/
├── scripts/
│   └── detect-impact.py        # CI/CD impact detection & capability mapping
└── src/
    ├── automation.py           # Infrastructure provisioning & workflow automation
    ├── backup.py                # Backup scheduling, execution, validation, reporting
    ├── deploy.py                 # Network, Kubernetes, AI, data platform deployment
    ├── dr_platform.py            # Disaster recovery orchestration
    ├── security_vault.py         # Vault namespace and key management
    └── service_broker.py         # Service catalog and API management
```

## 13.3 Configuration Management

`scripts/detect-impact.py` reads a YAML-based path-to-capability mapping (`read_yaml`) to drive capability impact resolution (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`), enabling configuration-driven documentation refresh rather than hard-coded logic.

## 13.4 Deployment Workflow

1. Pull request raised against `main` branch of `jijeeshlearningorg/greenfield-code`.
2. `scripts/detect-impact.py` executes: reads changed files, normalizes paths, resolves impacted capabilities against configured path mapping, and builds a documentation request payload (`build_doc_request`).
3. Payload written to JSON (`write_json`) for consumption by the documentation generation pipeline.
4. Platform deployment functions (`deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability`) executed in sequence by the orchestration engine for environment build-out.
5. Automation functions (`provision_infrastructure` → `execute_platform_workflow` → `deploy_configuration_baseline` → `validate_automation_results`) applied for infrastructure provisioning and configuration baselining.

## 13.5 Input Parameters

| Parameter | Purpose |
|----------|----------|
| `environment_name` | Target environment for infrastructure provisioning and configuration baseline (`provision_infrastructure`, `deploy_configuration_baseline`) |
| `workflow_name` | Identifies platform automation workflow to execute/validate (`execute_platform_workflow`, `validate_automation_results`) |
| `region` | Target region for network foundation deployment (`deploy_network_foundation`) |
| `cluster_name` | Target Kubernetes cluster for platform deployment (`deploy_kubernetes_platform`) |
| `environment` | Target environment for AI/data platform deployment and observability validation (`deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`) |
| `workload_name` | Target workload for backup scheduling/execution (`schedule_backup_job`, `execute_backup`) |
| `backup_id` | Identifier for backup integrity validation (`validate_backup_integrity`) |
| `application_name` | Target application for DR recovery plan/validation (`create_recovery_plan`, `validate_recovery_objectives`) |
| `target_site` | Destination site for DR failover (`execute_site_failover`) |
| `namespace_name` | Vault namespace identifier (`create_vault_namespace`) |
| `key_name` | Encryption key identifier (`create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`) |
| `service_name` | Service to which a key is assigned (`assign_key_to_service`) |
| `policy_name` | Vault policy identifier for validation (`validate_vault_policy`) |
| `catalog_name` | Service catalog identifier (`publish_service_catalog`) |
| `api_name` | Platform API identifier for registration (`register_platform_api`) |
| `service_name` (offering) | Service offering identifier (`create_service_offering`) |
| `subscription_id` | API subscription identifier for validation (`validate_api_subscription`) |
| `path` (mapping/changed files) | File paths supplied to `read_yaml`/`read_changed_files`/`write_json` in impact detection |
| `changed_files`, `mapping` | Inputs to `build_impacted_capabilities`, `build_doc_request` for capability impact resolution |

---

# 14. Monitoring & Operational Design

## 14.1 Monitoring

- **Metrics:** Infrastructure and workload performance metrics collected via VMware Aria Operations.
- **Dashboards:** Aria Operations dashboards providing capacity, performance, and health visibility across compute, storage, and network domains.
- **Observability Validation:** `validate_platform_observability(environment)` confirms monitoring, logging, and observability configuration post-deployment.

## 14.2 Logging

- Centralized log aggregation via Aria Logs across ESXi, vCenter, NSX-T, Vault, and Kubernetes components.
- Network-specific analytics via Aria Network Insight.

## 14.3 Alerting

Alerting policies configured within Aria Operations, triggered by threshold breaches on compute/storage/network metrics; automation validation failures (`validate_automation_results` returning `False`) should trigger pipeline/orchestration alerts.

## 14.4 Operational Ownership

| Function Area | Owning Team (Inferred) |
|----------|----------|
| Compute/Storage/Network (vSphere, vSAN, NSX-T) | Platform Infrastructure Team |
| Automation (`src/automation.py`, `src/deploy.py`) | Platform Automation/DevOps Team |
| Backup (`src/backup.py`) | Data Protection Team |
| Disaster Recovery (`src/dr_platform.py`) | Resilience/DR Team |
| Security & Vault (`src/security_vault.py`) | Security Engineering Team |
| Service Broker (`src/service_broker.py`) | API/Service Management Team |
| CI/CD Impact Detection (`scripts/detect-impact.py`) | DevOps/Documentation Automation Team |

---

# 15. Validation & Testing

## 15.1 Component Testing

Each function in `src/*.py` should be unit-tested independently given their stateless, single-responsibility design (e.g., `create_customer_managed_key`, `schedule_backup_job`).

## 15.2 Integration Testing

Validate end-to-end sequences: `deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability`; and `provision_infrastructure` → `execute_platform_workflow` → `deploy_configuration_baseline` → `validate_automation_results`.

## 15.3 Performance Testing

Performance validation of provisioning and deployment workflows against defined SLAs for environment build-out time.

## 15.4 Security Testing

Validate vault policy enforcement (`validate_vault_policy`), key rotation (`rotate_encryption_key`), and API subscription validation (`validate_api_subscription`); Nessus vulnerability scanning integrated into test cycle.

## 15.5 Failover Testing

Execute `execute_site_failover` in controlled test scenarios, validating recovery objectives via `validate_recovery_objectives`.

## 15.6 Disaster Recovery Testing

Periodic DR testing using `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, and `generate_dr_readiness_report` to confirm RPO/RTO compliance.

## 15.7 Operational Acceptance Testing

Validate observability (`validate_platform_observability`), backup integrity (`validate_backup_integrity`), and automation result validation (`validate_automation_results`) as acceptance gates prior to production handover.

---

# 16. Lifecycle Management

## 16.1 Patch Management

ESXi/vCenter/NSX-T patching managed via vSphere Lifecycle Manager (vLCM) and SDDC Manager; configuration baseline re-applied post-patch via `deploy_configuration_baseline`.

## 16.2 Upgrade Strategy

Platform upgrades (SDDC Manager-orchestrated) followed by automation validation (`validate_automation_results`) and observability validation (`validate_platform_observability`) to confirm post-upgrade health.

## 16.3 Rollback Strategy

Rollback of configuration baselines via re-execution of `deploy_configuration_baseline` against last-known-good parameters; DR recovery plans (`create_recovery_plan`) provide a fallback path for catastrophic upgrade failure.

## 16.4 Decommissioning

Service offerings and API registrations retired via inverse operations of `create_service_offering`/`register_platform_api` (deregistration processes, not explicitly present in repository — flagged as a gap); backup retention governed by `generate_backup_report` outputs prior to workload decommission.

---

# 17. Performance & Capacity Planning

| Resource | Requirement |
|----------|----------|
| CPU | Sized per vSphere cluster capacity planning standards for management and workload domains (not explicitly specified in repository — to be defined per environment) |
| Memory | Sized per vSAN/vSphere HA admission control policy (to be defined per environment) |
| Storage | vSAN capacity sized per workload + backup retention requirements (Data Domain sizing to be defined) |
| Bandwidth | NSX-T overlay/underlay bandwidth sized per north-south and east-west traffic profiles; HCX bandwidth sized for hybrid workload mobility |

---

# 18. RAID Register

| Type | Description | Owner | Mitigation |
|----------|----------|----------|----------|
| Risk | No explicit certificate/PKI management module detected in repository | Security Architect | Define and implement certificate management automation aligned to `src/security_vault.py` pattern |
| Risk | No class-based abstraction may limit extensibility as automation complexity grows | Platform Engineering | Evaluate refactoring to service-object pattern if function count/complexity increases materially |
| Assumption | CI/CD platform is GitHub Actions based, invoking `scripts/detect-impact.py` on PR events | DevOps Team | Confirm actual CI/CD platform configuration |
| Assumption | Aria Automation/Orchestrator is the invoking orchestrator for `src/*.py` functions | Platform Automation Team | Confirm orchestration binding during build phase (BIG) |
| Issue | No automated test suite detected in repository for `src/*.py` or `scripts/detect-impact.py` | Platform Engineering | Introduce unit/integration test coverage per Section 15 |
| Dependency | Backup and DR modules depend on external platforms (Canopy, Avamar, Data Domain, SRM) not present in repository | Data Protection / DR Team | Confirm integration credentials and endpoints during build |

---

# 19. Open Questions

| Question | Owner | Due Date |
|----------|----------|----------|
| What is the confirmed CI/CD platform executing `scripts/detect-impact.py`? | DevOps Team | TBD |
| What orchestration engine (Aria Orchestrator vs. custom scheduler) invokes `src/*.py` functions in production? | Platform Automation Team | TBD |
| Is certificate/PKI management to be added as a new module alongside `security_vault.py`? | Security Architect | TBD |
| What are the target RPO/RTO values validated by `validate_recovery_objectives`? | DR Team | TBD |
| What retention policy governs backup reports generated by `generate_backup_report`? | Data Protection Team | TBD |

---

# 20. Appendices

## 20.1 Configuration Parameters

| Parameter | Value | Description |
|----------|----------|----------|
| Source Repository | `jijeeshlearningorg/greenfield-code` | Repository analyzed for this LLD |
| Source Branch | `main` | Branch analyzed |
| Product | `my-cloud-platform` | Target product for this design |
| Files Scanned | 8 | Total files analyzed in repository scan |
| Functions Detected | 41 | Total functions across `src/` and `scripts/` |
| Classes Detected | 0 | No class-based structures present |
| Imports Detected | 4 | External/standard library imports across scanned files |

## 20.2 Naming Standards

Function naming follows a `verb_noun` convention (e.g., `create_vault_namespace`, `execute_site_failover`, `validate_backup_integrity`), indicating an action-oriented automation interface style consistently applied across all six `src/` modules.

## 20.3 IP Address Plan

Not defined in repository; to be documented as part of environment-specific network build (NSX-T segment/gateway CIDR allocation per `deploy_network_foundation`).

## 20.4 Ports & Protocols

| Source | Destination | Port | Protocol | Purpose |
|----------|----------|----------|----------|----------|
| Automation Engine | vCenter API | 443 | HTTPS | Infrastructure provisioning/workflow execution |
| Automation Engine | NSX-T Manager API | 443 | HTTPS | Network foundation deployment |
| Automation Engine | HashiCorp Vault API | 8200 | HTTPS | Secrets/key management operations |
| Automation Engine | Backup Platform API | 443 | HTTPS | Backup scheduling/execution/validation |
| Automation Engine | SRM/vSphere Replication API | 443 | HTTPS | DR plan creation/failover execution |
| Automation Engine | Service Broker/API Gateway | 443 | HTTPS | Catalog publishing/API registration |
| CI/CD Runner | GitHub Repository API | 443 | HTTPS | Changed file/PR metadata retrieval |

## 20.5 Glossary

| Term | Definition |
|----------|----------|
| HLD | High-Level Design |
| LLD | Low-Level Design |
| BIG | Build & Installation Guide |
| OPG | Operations Guide |
| ADR | Architecture Decision Record |
| IAM | Identity & Access Management |
| RBAC | Role-Based Access Control |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| SDDC | Software-Defined Data Center |
| VCF | VMware Cloud Foundation |
| TKG | Tanzu Kubernetes Grid |
| NSX-T | VMware NSX-T Data Center (networking/security platform) |
| vSAN | VMware vSAN (software-defined storage) |
| vLCM | vSphere Lifecycle Manager |
| SRM | Site Recovery Manager |
| HCX | Hybrid Cloud Extension (workload mobility platform) |
| VMC | VMware Cloud |
| CI/CD | Continuous Integration / Continuous Deployment |
