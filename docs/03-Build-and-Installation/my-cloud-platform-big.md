# Build & Installation Guide (BIG): My Cloud Services (my-cloud-platform)

**Author:** Platform Engineering Architecture Team  
**Date:** Generated from repository analysis of `jijeeshlearningorg/greenfield-code` (branch: `main`)  
**Version:** 1.0  
**Status:** Draft  
**Owner:** Platform Engineering / Cloud Infrastructure Team

---

# 1. Document Control

## 1.1 Review & Approval

| Role | Name | Status |
|--------|--------|--------|
| Reviewer | Platform Engineering Lead | Pending |
| Security Review | Security & Compliance Team | Pending |
| Document Owner | Cloud Platform Owner | Pending |

## 1.2 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | Initial Generation | Baseline Build & Installation Guide generated from source repository analysis (`scripts/detect-impact.py`, `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) | Platform Engineering Architect |

---

# 2. Introduction

## 2.1 Purpose

This document describes the build, installation, configuration, validation, rollback and operational handover procedures for **My Cloud Services**, a VMware-based private/hybrid cloud platform. The platform automation is implemented in the `greenfield-code` repository and covers infrastructure provisioning, network and Kubernetes platform deployment, AI/data platform enablement, security/vault key management, backup orchestration, disaster recovery, and API service brokering.

The guide is derived directly from the repository's automation modules (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) and the CI/CD impact-detection tooling (`scripts/detect-impact.py`).

## 2.2 Audience

- Platform Engineers responsible for build and deployment execution
- Cloud/Infrastructure Automation Teams
- Security and Vault Operations Teams
- Operations and Support Teams performing day-2 operations
- Service Delivery / API Service Broker Teams

## 2.3 Scope

### In Scope

- Installation and provisioning of the VMware-based cloud platform stack (compute, storage, networking)
- Configuration of automation workflows (`src/automation.py`)
- Deployment of network, Kubernetes, AI and data platform components (`src/deploy.py`)
- Backup job scheduling and validation (`src/backup.py`)
- Disaster recovery plan creation and failover validation (`src/dr_platform.py`)
- Security vault namespace, key management and policy validation (`src/security_vault.py`)
- Service catalog publishing and API registration (`src/service_broker.py`)
- CI/CD impact detection and documentation automation (`scripts/detect-impact.py`)
- Validation, rollback and operational handover

### Out of Scope

- High-Level Design (HLD)
- Low-Level Design (LLD)
- Operational Procedures (OPG)

## 2.4 Related Documents

| Document Type | Document ID | Relationship |
|----------|----------|----------|
| HLD | My Cloud Services – Architecture Design | Architecture Design |
| LLD | My Cloud Services – Detailed Design | Detailed Design |
| BIG | This Document | Current Document |
| OPG | My Cloud Services – Operations Guide | Operations Guide |
| ADR | Architecture Decision Records (repository `main` branch) | Architecture Decisions |
| Runbooks | Automation Module Runbooks (`src/automation.py`, `src/backup.py`, `src/dr_platform.py`) | Operational Procedures |
| Vendor Documentation | VMware vSphere / NSX-T / Aria Suite / Tanzu Documentation | Product Reference |

---

# 3. Deployment Context

- System Type: VMware-based Private/Hybrid Cloud Platform (Software-Defined Data Center)
- Deployment Model: Automated, script-driven deployment using Python automation modules with CI/CD-driven impact detection
- Platform/Provider: VMware vSphere, vSAN, NSX-T, Aria Suite, Tanzu Kubernetes Grid, with optional public cloud integration (VMC)
- Environment: Supports multi-environment automation (e.g., `dev`, `staging`, `prod`) driven by environment-name parameters passed to automation functions (`provision_infrastructure(environment_name)`, `deploy_configuration_baseline(environment_name)`)

---

# 4. Package / Build Description

## 4.1 Package Overview

The `greenfield-code` repository provides the automation codebase for **My Cloud Services**. It is organized into discrete Python modules under `src/`, each responsible for a functional domain of the cloud platform, plus a CI/CD impact-detection utility under `scripts/`. The modules do not contain classes (0 classes detected); all logic is implemented as top-level functions (41 functions detected across 8 files).

## 4.2 Product / Platform Components

| Component | Source / Location |
|----------|----------|
| Automation Orchestration Engine | `src/automation.py` (`provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`) |
| Platform Deployment Engine | `src/deploy.py` (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`) |
| Backup Service Module | `src/backup.py` (`schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`) |
| Disaster Recovery Module | `src/dr_platform.py` (`create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`) |
| Security & Vault Module | `src/security_vault.py` (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`) |
| Service Broker Module | `src/service_broker.py` (`publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`) |
| CI/CD Impact Detection | `scripts/detect-impact.py` (15 functions including `resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request`) |
| Compute Platform | vSphere / ESXi (technology catalog) |
| Storage Platform | vSAN, optional Fibre Channel storage |
| Networking Platform | NSX-T |
| Automation/Orchestration Platform | Aria Automation, Aria Orchestrator |
| Monitoring Platform | Aria Operations, Aria Logs, Aria Network Insight |
| Container Platform | Tanzu Kubernetes Grid, Tanzu Mission Control |
| Lifecycle Management | SDDC Manager, vSphere Lifecycle Manager (vLCM), Aria Suite Lifecycle Manager |
| Security Tooling | Trend Micro, Nessus, HashiCorp Vault |
| Backup Platform | Canopy Enterprise Backup, Avamar, Data Domain |
| Disaster Recovery Platform | Site Recovery Manager (SRM), vSphere Replication |
| Workload Mobility | HCX |
| Public Cloud Integration | VMware Cloud (VMC) |
| Service Delivery | Service Broker |

## 4.3 Versioning

| Item | Version / Reference |
|----------|----------|
| Repository | `jijeeshlearningorg/greenfield-code` |
| Branch | `main` |
| Automation Modules | Version tracked via repository commit history (no explicit version constants detected in source) |
| Underlying Platform Technologies | vSphere/ESXi/vCenter/vSAN/NSX-T/Aria Suite/Tanzu — versions to be recorded at deployment time per environment build sheet |

## 4.4 Installation Notes

- All deployment functions return boolean success indicators (e.g., `deploy_network_foundation(region) -> bool`), implying installation steps must be gated on function return values before proceeding to dependent steps.
- `src/deploy.py` functions have interdependencies across domains: networking must be validated before Kubernetes, AI, and data platform deployment (inferred from function ordering: `deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability`).
- `scripts/detect-impact.py` is used in CI/CD pipelines to determine which platform capabilities are impacted by a given code change (via `resolve_capabilities_for_changed_file` and `build_impacted_capabilities`), and should be run as part of the pull request pipeline before merge to `main`.
- No explicit configuration files (YAML/JSON) were found in the scanned repository beyond what `read_yaml()` in `scripts/detect-impact.py` consumes; the YAML mapping file consumed by this function is an external dependency and must be provisioned (inferred).
- No classes were detected; all modules operate as function libraries — deployment orchestration (e.g., an entry-point pipeline or scheduler) invoking these functions in sequence is an external dependency and must be confirmed operationally.

---

# 5. Pre-Requisites

## 5.1 Infrastructure

- Compute: vSphere/ESXi cluster capacity sized for platform workloads (compute domain)
- Storage: vSAN datastore (software-defined storage) or Fibre Channel storage (optional)
- Network: NSX-T managed network fabric for segmentation, routing, and connectivity
- DNS: Enterprise DNS resolution for all platform management endpoints (vCenter, NSX Manager, Aria Suite, Tanzu)
- NTP: Time synchronization service across all platform components (required for vSphere/NSX-T/vSAN cluster health)
- Backup Infrastructure: Canopy Enterprise Backup / Avamar with Data Domain target storage

## 5.2 Hardware Requirements

- CPU: Sized per vSphere cluster capacity plan (per-environment sizing required)
- Memory: Sized per vSAN/vSphere workload domain requirements
- Storage: vSAN-eligible disk groups (cache + capacity tier) or Fibre Channel LUNs
- Rack Requirements: Per data center standard rack/power/cooling specification
- BIOS Settings: Virtualization extensions enabled (VT-x/AMD-V), power management set per VMware best practice

## 5.3 Software Requirements

- Operating Systems: ESXi hypervisor OS on all compute hosts
- Middleware: Aria Automation, Aria Orchestrator (automation/orchestration layer used by `src/automation.py` workflows)
- Runtime Components: Python runtime for executing `src/*.py` automation modules and `scripts/detect-impact.py`
- Libraries: Python standard library modules for YAML/JSON parsing (as used by `read_yaml`, `write_json` in `scripts/detect-impact.py`) — `PyYAML` dependency inferred
- Drivers: ESXi-certified hardware drivers per VMware Compatibility Guide
- Utilities: Git (for repository/CI-CD change detection), CI/CD runner (e.g., pipeline executing `scripts/detect-impact.py`)

## 5.4 Access & Permissions

| Role | Permissions | Notes |
|----------|----------|----------|
| Platform Automation Service Account | Administrator on vCenter, NSX-T Manager, Aria Automation | Used by `provision_infrastructure` and `execute_platform_workflow` |
| Security/Vault Administrator | Vault namespace and key management rights | Used by `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key` |
| Backup Operator | Backup scheduling and execution rights on backup platform | Used by `schedule_backup_job`, `execute_backup` |
| DR Operator | Recovery plan and failover execution rights on SRM/vSphere Replication | Used by `create_recovery_plan`, `execute_site_failover` |
| Service Broker Administrator | Catalog publishing and API registration rights | Used by `publish_service_catalog`, `register_platform_api` |
| CI/CD Pipeline Service Account | Read access to repository, write access to documentation/output artifacts | Used by `scripts/detect-impact.py` |

## 5.5 Security Requirements

- Security Baselines: VMware vSphere/NSX-T hardening guides applied to all hosts and management components
- Encryption Requirements: Customer-managed encryption keys enforced via `create_customer_managed_key` and `assign_key_to_service` (HashiCorp Vault backend)
- Compliance Requirements: Endpoint protection (Trend Micro) and vulnerability scanning (Nessus) enabled prior to production cutover
- Hardening Standards: Vault policies validated via `validate_vault_policy` prior to service key assignment

## 5.6 Secrets & Credential Dependencies

| Credential Type | Purpose | Storage Location |
|----------|----------|----------|
| vCenter/NSX-T Service Account Credentials | Automation execution (`provision_infrastructure`, `deploy_network_foundation`) | HashiCorp Vault |
| Vault Root/Namespace Tokens | Vault namespace creation and key operations (`create_vault_namespace`) | HashiCorp Vault (self-managed) |
| Customer-Managed Encryption Keys | Service encryption (`create_customer_managed_key`, `assign_key_to_service`) | HashiCorp Vault |
| Backup Platform Credentials | Backup job execution (`execute_backup`) | Canopy Enterprise Backup / Avamar credential store |
| DR Platform Credentials | Site failover execution (`execute_site_failover`) | SRM/vSphere Replication credential store |
| Service Broker API Keys | API registration and subscription validation (`register_platform_api`, `validate_api_subscription`) | Service Broker secrets store |
| CI/CD Pipeline Tokens | Repository access, PR metadata retrieval (`get_pull_request_number`, `get_pull_request_url`) | CI/CD platform secrets store |

## 5.7 Certificate Requirements

| Certificate | Purpose | Owner |
|----------|----------|----------|
| vCenter/NSX-T Management SSL Certificate | Secure management plane access | Platform Engineering |
| Aria Suite Component Certificates | Secure automation/orchestration/monitoring endpoints | Platform Engineering |
| Vault TLS Certificate | Secure vault namespace and key operations | Security Team |
| Service Broker API Certificate | Secure external API endpoint exposure | API Service Broker Team |

## 5.8 Firewall & Network Dependencies

- Firewall Rules: Management plane access between automation host and vCenter/NSX-T/Aria Suite/Vault endpoints
- Proxy Requirements: Outbound proxy configuration for CI/CD pipeline access to repository and external registries (if applicable)
- Load Balancer Dependencies: Load balancing for Aria Automation, Service Broker, and Tanzu Kubernetes Grid control plane endpoints
- Required Ports: Standard VMware management ports (vCenter 443, NSX-T Manager 443, Aria Suite 443, Vault 8200) — to be confirmed against environment build sheet
- External Endpoints: HashiCorp Vault, Data Domain backup target, SRM peer site, VMC (if public cloud integration enabled)

## 5.9 External Dependencies

- Active Directory: Identity source for platform RBAC
- LDAP: Directory integration for vCenter/NSX-T/Aria Suite authentication
- DNS: Enterprise DNS as described in Section 5.1
- Monitoring Platform: Aria Operations, Aria Logs, Aria Network Insight (validated via `validate_platform_observability`)
- Backup Platform: Canopy Enterprise Backup, Avamar, Data Domain
- Vault Solution: HashiCorp Vault (`src/security_vault.py`)
- External APIs: Platform APIs registered via `register_platform_api`
- Database Platforms: Data platform backend (deployed via `deploy_data_platform`) — specific database engine not specified in source (inferred requirement)
- Message Queues: Not evidenced in repository — to be confirmed with architecture team

## 5.10 Licensing Requirements

- Product Licenses: VMware vSphere, vSAN, NSX-T, Aria Suite, Tanzu Kubernetes Grid, Tanzu Mission Control
- Subscription Entitlements: HashiCorp Vault Enterprise, Canopy Enterprise Backup, Trend Micro, Nessus
- License Keys: To be provisioned per environment build sheet prior to installation (not stored in repository)

## 5.11 Skills Required

| Skill | Level |
|----------|----------|
| VMware vSphere/vSAN/NSX-T Administration | Advanced |
| VMware Aria Suite (Automation/Orchestrator/Operations/Logs) | Advanced |
| Tanzu Kubernetes Grid / Kubernetes Operations | Intermediate–Advanced |
| Python Scripting (automation module execution/maintenance) | Intermediate |
| HashiCorp Vault Administration | Intermediate–Advanced |
| Backup & DR Operations (SRM, Avamar, Data Domain) | Intermediate |
| CI/CD Pipeline Administration (GitHub Actions or equivalent) | Intermediate |

---

# 6. Input Parameters

| Parameter | Value | Description |
|----------|----------|----------|
| `environment_name` | e.g., `dev`, `staging`, `prod` | Target environment for `provision_infrastructure`, `deploy_configuration_baseline` |
| `workflow_name` | Platform-defined workflow identifier | Input to `execute_platform_workflow` and `validate_automation_results` |
| `region` | Target deployment region | Input to `deploy_network_foundation` |
| `cluster_name` | Kubernetes cluster identifier | Input to `deploy_kubernetes_platform` |
| `environment` (AI/Data) | Target environment for AI/Data platform | Input to `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability` |
| `workload_name` | Workload identifier for backup | Input to `schedule_backup_job`, `execute_backup` |
| `backup_id` | Identifier of executed backup | Input to `validate_backup_integrity` |
| `application_name` | Application identifier for DR | Input to `create_recovery_plan`, `validate_recovery_objectives` |
| `target_site` | DR target site identifier | Input to `execute_site_failover` |
| `namespace_name` | Vault namespace identifier | Input to `create_vault_namespace` |
| `key_name` | Encryption key identifier | Input to `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service` |
| `service_name` | Platform service identifier | Input to `assign_key_to_service` |
| `policy_name` | Vault policy identifier | Input to `validate_vault_policy` |
| `catalog_name` | Service catalog identifier | Input to `publish_service_catalog` |
| `api_name` | Platform API identifier | Input to `register_platform_api` |
| `service_name` (Broker) | Self-service offering identifier | Input to `create_service_offering` |
| `subscription_id` | API subscription identifier | Input to `validate_api_subscription` |
| YAML mapping file path | Path to capability/path mapping file | Input to `read_yaml` in `scripts/detect-impact.py` |
| Changed files list path | Path to CI/CD changed-files manifest | Input to `read_changed_files` in `scripts/detect-impact.py` |

---

# 7. Build Overview

## 7.1 Deployment Flow

```text
Prepare → Install → Configure → Validate → Handover
```

## 7.2 Build Phases

- Preparation: Infrastructure readiness, credentials/secrets in Vault, network and DNS/NTP prerequisites confirmed
- Installation: Execution of `provision_infrastructure`, `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`
- Configuration: Execution of `deploy_configuration_baseline`, vault namespace/key setup (`create_vault_namespace`, `create_customer_managed_key`, `assign_key_to_service`), service broker setup (`publish_service_catalog`, `register_platform_api`, `create_service_offering`)
- Integration: Backup scheduling (`schedule_backup_job`), DR recovery plan creation (`create_recovery_plan`), API subscription validation (`validate_api_subscription`)
- Validation: `validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`

---

# 8. Installation Procedure

## 8.1 Installation Overview

Installation is automated and script-driven, executed via Python automation modules within the `greenfield-code` repository. Each module exposes discrete functions returning boolean success indicators, allowing installation orchestration (manual invocation, scheduler, or CI/CD pipeline) to gate subsequent steps on prior step success. No manual GUI-based installation steps are evidenced in the repository; all documented actions are automation-module driven.

## 8.2 Step-by-Step Installation

| Step | Action | Estimated Duration | Notes |
|----------|----------|----------|----------|
| 1 | Confirm infrastructure and credential prerequisites (Section 5) are met | 1–2 hours | Manual pre-check |
| 2 | Execute `provision_infrastructure(environment_name)` from `src/automation.py` | Variable (environment-dependent) | Provisions base infrastructure for target environment |
| 3 | Execute `deploy_network_foundation(region)` from `src/deploy.py` | Variable | Deploys core NSX-T networking components |
| 4 | Execute `deploy_kubernetes_platform(cluster_name)` from `src/deploy.py` | Variable | Deploys Tanzu Kubernetes Grid platform services |
| 5 | Execute `deploy_ai_platform(environment)` from `src/deploy.py` | Variable | Deploys AI platform and model hosting infrastructure |
| 6 | Execute `deploy_data_platform(environment)` from `src/deploy.py` | Variable | Deploys enterprise data services and analytics platform |
| 7 | Execute `deploy_configuration_baseline(environment_name)` from `src/automation.py` | Variable | Applies standard platform configuration baselines |
| 8 | Execute `execute_platform_workflow(workflow_name)` from `src/automation.py` | Variable | Executes required platform automation workflows |
| 9 | Validate installation with `validate_automation_results(workflow_name)` and `validate_platform_observability(environment)` | 30–60 minutes | Confirms automation success and monitoring/logging configuration |

## 8.3 Platform-Specific Steps

- VMware: Ensure vCenter, ESXi, and vSAN cluster prerequisites (Section 5.1/5.2) are satisfied prior to Step 2.
- NSX-T: Confirm NSX Manager and transport zone configuration prior to Step 3 (`deploy_network_foundation`).
- Tanzu: Confirm Tanzu Mission Control registration readiness prior to Step 4 (`deploy_kubernetes_platform`).
- Aria Suite: Confirm Aria Automation/Orchestrator availability prior to Step 2 and Step 7 (used by `src/automation.py` workflows).

---

# 9. Deployment Procedure

## 9.1 Deployment Overview

Deployment follows a phased, function-driven strategy: network foundation is deployed first, followed by compute/Kubernetes, AI, and data platform layers, with configuration baselines and validation applied at each stage. Deployment execution is gated on boolean success return values from each automation function.

## 9.2 Deployment Steps

- Provisioning: `provision_infrastructure(environment_name)` (`src/automation.py`)
- Installation: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` (`src/deploy.py`)
- Configuration: `deploy_configuration_baseline(environment_name)` (`src/automation.py`); vault setup via `create_vault_namespace`, `create_customer_managed_key`, `assign_key_to_service` (`src/security_vault.py`)
- Validation: `validate_automation_results(workflow_name)` (`src/automation.py`), `validate_platform_observability(environment)` (`src/deploy.py`), `validate_vault_policy(policy_name)` (`src/security_vault.py`)

## 9.3 Validation Plan

### Health Checks

- Service Status Validation: `validate_automation_results(workflow_name)` confirms automation workflow execution outcome
- Component Health Validation: `validate_platform_observability(environment)` confirms monitoring, logging and observability configuration

### Connectivity Tests

- Network Validation: Confirm NSX-T connectivity following `deploy_network_foundation(region)` execution
- External Dependency Validation: Confirm connectivity to Vault, Backup Platform, and DR site endpoints

### Functional Validation

- Core Function Verification: Confirm Kubernetes, AI, and data platform services are operational post-deployment (`deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`)
- Integration Testing: `validate_api_subscription(subscription_id)` (`src/service_broker.py`) to confirm API consumer integration
- User Acceptance Testing: Service catalog offering verification via `create_service_offering` and `validate_api_subscription`

## 9.4 Acceptance Criteria

The deployment is considered successful when:

- Installation completed successfully (Section 8.2 steps executed with `True` return values)
- Services operational (network, Kubernetes, AI, data platform layers validated)
- Validation completed successfully (`validate_automation_results`, `validate_platform_observability`, `validate_vault_policy`)
- Dependencies operational (Vault, Backup, DR platform connectivity confirmed)
- Customer acceptance completed (Section 15.3 sign-off obtained)

---

# 10. Configuration Steps

## 10.1 System Configuration

- Operating System: ESXi host configuration per VMware hardening baseline
- Network Settings: NSX-T segment, routing and firewall configuration applied via `deploy_network_foundation`
- Storage Configuration: vSAN datastore configuration and policy assignment (supporting `deploy_data_platform`)

## 10.2 Security Configuration

- RBAC: Role assignment for automation service accounts (Section 5.4)
- IAM: Vault namespace-based identity segregation via `create_vault_namespace`
- Certificates: TLS certificate deployment per Section 5.7
- Hardening: Endpoint protection (Trend Micro) and vulnerability scanning (Nessus) applied
- Audit Configuration: Vault policy validation via `validate_vault_policy(policy_name)`; key lifecycle operations via `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`

## 10.3 Integration Configuration

- APIs: `register_platform_api(api_name)` (`src/service_broker.py`)
- External Systems: Backup platform integration via `schedule_backup_job`/`execute_backup`; DR platform integration via `create_recovery_plan`/`execute_site_failover`
- Monitoring Platforms: Aria Operations/Logs/Network Insight validated via `validate_platform_observability`
- Backup Platforms: Canopy Enterprise Backup/Avamar/Data Domain configured via `src/backup.py` functions

---

# 11. Post-Installation Tasks

- Monitoring Configuration: Confirm Aria Operations/Aria Logs dashboards active following `validate_platform_observability`
- Backup Configuration: Confirm scheduled backup jobs via `schedule_backup_job(workload_name)` and validate integrity via `validate_backup_integrity(backup_id)`
- Documentation Updates: Update CMDB and architecture documentation to reflect deployed component versions (Section 4.3)
- CMDB Updates: Register all newly provisioned infrastructure, network and Kubernetes components
- Operations Handover: Complete Section 15 handover procedures

---

# 12. Troubleshooting

| Issue | Cause | Resolution |
|----------|----------|----------|
| `provision_infrastructure(environment_name)` returns `False` | Infrastructure prerequisites not met (compute/storage/network capacity or credentials) | Verify Section 5.1–5.4 prerequisites, review automation logs, re-execute after remediation |
| `deploy_network_foundation(region)` returns `False` | NSX-T Manager unreachable or misconfigured transport zone | Validate NSX-T Manager connectivity and transport zone configuration prior to retry |
| `deploy_kubernetes_platform(cluster_name)` returns `False` | Tanzu Kubernetes Grid dependency (network foundation) not deployed or unhealthy | Confirm `deploy_network_foundation` succeeded before retrying Kubernetes deployment |
| `validate_platform_observability(environment)` returns `False` | Aria Operations/Aria Logs integration not configured | Verify monitoring platform connectivity and reattempt validation |
| `execute_backup(workload_name)` fails | Backup platform (Avamar/Data Domain) unreachable or credentials invalid | Verify backup platform credentials and connectivity (Section 5.6, 5.9) |
| `validate_backup_integrity(backup_id)` returns `False` | Backup completed with data integrity issues | Review backup job logs, re-run `execute_backup`, escalate to backup platform team if recurring |
| `execute_site_failover(target_site)` fails | SRM/vSphere Replication link down or DR site unavailable | Verify SRM pairing and replication status before retrying failover |
| `create_vault_namespace(namespace_name)` fails | Vault service unreachable or insufficient permissions | Verify Vault connectivity and administrator credentials (Section 5.4, 5.6) |
| `validate_vault_policy(policy_name)` returns `False` | Policy misconfigured or not assigned to namespace | Review Vault policy definitions and reassign as required |
| `register_platform_api(api_name)` fails | Service Broker unreachable or API name conflict | Verify Service Broker availability, confirm unique API naming, retry registration |
| `scripts/detect-impact.py` fails to resolve capabilities | Malformed or missing YAML mapping file (`read_yaml`) | Verify mapping file path and structure supplied to `read_yaml` |

---

# 13. Rollback Procedure

## 13.1 Conditions

- Failure Scenarios: Any Step in Section 8.2 returning `False`, failed validation in Section 9.3, or post-deployment functional test failure
- Rollback Triggers: `validate_automation_results` returns `False`; `validate_platform_observability` returns `False`; `validate_backup_integrity` or `validate_recovery_objectives` fails after cutover; security policy validation failure (`validate_vault_policy`)

## 13.2 Steps

- Backup Restoration: Use platform backup (Canopy Enterprise Backup/Avamar/Data Domain) to restore prior configuration state; reference `generate_backup_report()` output for last known-good backup
- Configuration Reversal: Revert configuration baseline changes applied via `deploy_configuration_baseline`; remove vault keys/namespaces created via `create_vault_namespace`/`create_customer_managed_key` if issued in error
- Validation Activities: Re-run `validate_automation_results`, `validate_platform_observability`, and `validate_vault_policy` post-rollback to confirm platform returned to last stable state before re-attempting deployment

---

# 14. Known Issues

```text
No known issues at the time of publication.
```

---

# 15. Handover and Acceptance

## 15.1 Handover Artifacts

- Configuration Backup: Baseline configuration snapshot generated via `deploy_configuration_baseline`
- Deployment Logs: Execution logs from `provision_infrastructure`, `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`
- Validation Results: Outputs of `validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`
- Runbooks: Automation module reference (`src/automation.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`)
- Related Documentation: HLD, LLD, OPG (Section 2.4)

## 15.2 Ownership Transfer

- Operations Team: Assumes responsibility for `execute_platform_workflow`, `schedule_backup_job`, and ongoing monitoring
- Support Team: Assumes responsibility for troubleshooting (Section 12) and incident escalation
- Service Owner: Assumes overall accountability for platform availability and the service catalog (`publish_service_catalog`, `create_service_offering`)

## 15.3 Acceptance Sign-Off

| Role | Name | Date | Status |
|----------|----------|----------|----------|
| Deployment Lead | | | Pending |
| Service Owner | | | Pending |
| Operations | | | Pending |

### 15.4 Operations Readiness

| Item | Status |
|--------|--------|
| OPG Completed | Pending |
| Monitoring Configured | Pending (`validate_platform_observability`) |
| Alerting Configured | Pending |
| Backup Configured | Pending (`schedule_backup_job`) |
| Recovery Tested | Pending (`validate_recovery_objectives`) |
| Runbooks Delivered | Pending |
| Ownership Assigned | Pending |
| Escalation Process Defined | Pending |

---

# 16. Appendices

## 16.1 Ports & Protocols

| Source | Destination | Port | Protocol | Purpose |
|----------|----------|----------|----------|----------|
| Automation Host | vCenter | 443 | HTTPS | vSphere API access (`provision_infrastructure`) |
| Automation Host | NSX-T Manager | 443 | HTTPS | Network deployment (`deploy_network_foundation`) |
| Automation Host | Aria Automation/Orchestrator | 443 | HTTPS | Workflow execution (`execute_platform_workflow`) |
| Automation Host | HashiCorp Vault | 8200 | HTTPS | Secrets/key management (`src/security_vault.py`) |
| Automation Host | Backup Platform (Avamar/Data Domain) | 443/9000 (verify) | HTTPS/Proprietary | Backup operations (`src/backup.py`) |
| Automation Host | SRM/vSphere Replication | 443 | HTTPS | DR operations (`src/dr_platform.py`) |
| External Consumers | Service Broker API | 443 | HTTPS | API service consumption (`src/service_broker.py`) |
| CI/CD Runner | Source Repository | 443 | HTTPS | Impact detection (`scripts/detect-impact.py`) |

## 16.2 Network Plan

- VLANs: To be defined per environment build sheet (NSX-T segment mapping)
- Subnets: To be defined per environment build sheet
- Routing: NSX-T Tier-0/Tier-1 gateway configuration (deployed via `deploy_network_foundation`)
- Network Diagrams: Maintained in HLD/LLD (Section 2.4)
- Firewall Zones: Management, workload, and DMZ zones segregated via NSX-T micro-segmentation

## 16.3 Naming Standards

| Object Type | Naming Convention |
|----------|----------|
| Server | `<env>-<domain>-<role>-<sequence>` (e.g., `prod-compute-esxi-001`) |
| Database | `<env>-data-<service>` (aligned with `deploy_data_platform` environment parameter) |
| Network | `<env>-<region>-<segment>` (aligned with `deploy_network_foundation` region parameter) |

## 16.4 Glossary

| Term | Definition |
|----------|----------|
| API | Application Programming Interface |
| BIG | Build & Installation Guide |
| CI/CD | Continuous Integration / Continuous Delivery |
| DNS | Domain Name System |
| HLD | High-Level Design |
| IAM | Identity and Access Management |
| LLD | Low-Level Design |
| OPG | Operations Guide |
| PKI | Public Key Infrastructure |
| RBAC | Role-Based Access Control |
| NSX-T | VMware Software-Defined Networking and Security Platform |
| vSAN | VMware Software-Defined Storage Platform |
| SRM | VMware Site Recovery Manager |
| HCX | VMware Workload Mobility and Migration Platform |
| VMC | VMware Cloud (Public Cloud Integration) |
| Aria Suite | VMware Aria Automation, Orchestrator, Operations, Logs, Network Insight product family |
