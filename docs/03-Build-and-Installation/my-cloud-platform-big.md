# Build & Installation Guide (BIG): My Cloud Services (my-cloud-platform)

**Author:** Platform Engineering Architect (Generated)
**Date:** TBD - repository evidence not found.
**Version:** 1.0
**Status:** Draft
**Owner:** TBD - repository evidence not found.

---

# 1. Document Control

## 1.1 Review & Approval

| Role | Name | Status |
|--------|--------|--------|
| Reviewer | TBD - repository evidence not found. | Pending |
| Security Review | TBD - repository evidence not found. | Pending |
| Document Owner | TBD - repository evidence not found. | Pending |

## 1.2 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | TBD - repository evidence not found. | Initial generation from repository `jijeeshlearningorg/greenfield-code` (branch `main`) | Platform Engineering Architect (Generated) |

---

# 2. Introduction

## 2.1 Purpose

This document describes the build, installation, configuration, validation, rollback and handover procedures for **My Cloud Services**, derived from analysis of the `jijeeshlearningorg/greenfield-code` repository. The repository contains automation modules (`src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) and an impact-detection script (`scripts/detect-impact.py`) which together implement platform provisioning, deployment, backup, disaster recovery, security/vault, and service broker functions supporting the VMware Cloud Foundation-based capability set (compute, storage, networking, automation, monitoring, security, disaster-recovery, backup, containers, multi-tenancy, lifecycle-management, public-cloud-integration, reporting, api-service-broker).

## 2.2 Audience

- Platform Engineering Teams
- Automation/DevOps Engineers
- Operations Teams
- Security Teams
- Support Teams

## 2.3 Scope

### In Scope

- Installation of automation modules (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`)
- Configuration of platform baselines, network foundation, Kubernetes, AI, and data platform components
- Validation of automation, backup, observability, disaster-recovery readiness, vault policy and API subscription functions
- Rollback of deployment stages based on module relationships
- Operational handover of the deployed automation platform

### Out of Scope

- High-Level Design (HLD)
- Low-Level Design (LLD)
- Operational Procedures (OPG)

## 2.4 Related Documents

| Document Type | Document ID | Relationship |
|----------|----------|----------|
| HLD | TBD - repository evidence not found. | Architecture Design |
| LLD | TBD - repository evidence not found. | Detailed Design |
| BIG | Current Document | Current Document |
| OPG | TBD - repository evidence not found. | Operations Guide |
| ADR | TBD - repository evidence not found. | Architecture Decisions |
| Runbooks | TBD - repository evidence not found. | Operational Procedures |
| Vendor Documentation | TBD - repository evidence not found. | Product Reference |

---

# 3. Deployment Context

- System Type: Automation/orchestration codebase for cloud platform lifecycle (provisioning, deployment, backup, disaster recovery, security vault, service broker).
- Deployment Model: Script/module-driven automation invoked in sequence per `deployment_flow` (inferred from repository function ordering; no explicit CI/CD pipeline file detected in scanned files).
- Platform/Provider: VMware-based Cloud Services stack (vSphere, vSAN, NSX-T, Aria Suite, Tanzu, SDDC Manager) per Product Technologies catalog; source repository implements automation wrappers around these technologies.
- Environment: TBD - repository evidence not found. (No environment-specific configuration files were detected in the scanned repository.)

---

# 4. Package / Build Description

## 4.1 Package Overview

The repository `jijeeshlearningorg/greenfield-code` provides Python automation modules that implement the build/deploy/validate lifecycle for **My Cloud Services**. Modules are organized by domain function:

- `src/automation.py` – infrastructure provisioning and workflow execution (automation, lifecycle-management, observability, security domains)
- `src/deploy.py` – network, Kubernetes, AI, and data platform deployment (ai-platform, api-service-broker, compute, data-platform, kubernetes, lifecycle-management, networking, observability, security domains)
- `src/backup.py` – backup scheduling, execution and validation (backup, lifecycle-management, observability, security, storage domains)
- `src/dr_platform.py` – disaster recovery planning, failover and readiness reporting (ai-platform, backup, disaster-recovery, lifecycle-management, observability, security domains)
- `src/security_vault.py` – vault namespace, key management and policy validation (api-service-broker, automation, kubernetes, lifecycle-management, observability, security domains)
- `src/service_broker.py` – service catalog publishing, API registration and subscription validation (api-service-broker, lifecycle-management, observability, security domains)
- `scripts/detect-impact.py` – change-impact detection utility mapping changed files to capability domains (ai-platform, api-service-broker, automation, compute, data-platform, lifecycle-management domains)

## 4.2 Product / Platform Components

| Component | Source / Location |
|----------|----------|
| Automation Provisioning Module | `src/automation.py` |
| Deployment Module (network/Kubernetes/AI/data) | `src/deploy.py` |
| Backup Module | `src/backup.py` |
| Disaster Recovery Module | `src/dr_platform.py` |
| Security/Vault Module | `src/security_vault.py` |
| Service Broker Module | `src/service_broker.py` |
| Change Impact Detection Script | `scripts/detect-impact.py` |
| vSphere / ESXi / vCenter (underlying platform technology) | Product Technology Catalog |
| vSAN / NSX-T (underlying platform technology) | Product Technology Catalog |
| Aria Automation / Aria Orchestrator / Aria Operations / Aria Logs (underlying platform technology) | Product Technology Catalog |
| Tanzu Kubernetes Grid / Tanzu Mission Control (underlying platform technology) | Product Technology Catalog |
| HashiCorp Vault (underlying platform technology) | Product Technology Catalog |
| SRM / vSphere Replication (underlying platform technology) | Product Technology Catalog |

## 4.3 Versioning

TBD - repository evidence not found. (No version manifest, package metadata, or release tag information was present in the scanned files.)

## 4.4 Installation Notes

- All detected modules are Python source files (`ast_success` or `ast_failed_regex_fallback` parse status); no explicit dependency manager file (e.g., `requirements.txt`, `pyproject.toml`) was found in the scanned repository set.
- `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, and `src/service_broker.py` import the `logging` module only (per Module Relationships); no other third-party imports were detected.
- Installation sequencing must follow the `deployment_flow` order documented in Section 7 and Section 8, as this is the only build-sequencing evidence available in the repository.

---

# 5. Pre-Requisites

## 5.1 Infrastructure

- Compute: TBD - repository evidence not found. (Underlying compute capability documented in Product Capabilities as VMware vSphere based compute platform.)
- Storage: TBD - repository evidence not found. (Underlying storage capability documented as VMware vSAN / Fibre Channel per Product Capabilities.)
- Network: TBD - repository evidence not found. (Underlying networking capability documented as NSX-T based per Product Capabilities; implemented via `deploy_network_foundation` in `src/deploy.py`.)
- DNS: TBD - repository evidence not found.
- NTP: TBD - repository evidence not found.
- Backup Infrastructure: Implemented via `src/backup.py` functions (`schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`); underlying technologies per catalog include Canopy Enterprise Backup, Avamar, Data Domain.

## 5.2 Hardware Requirements

- CPU: TBD - repository evidence not found.
- Memory: TBD - repository evidence not found.
- Storage: TBD - repository evidence not found.
- Rack Requirements: TBD - repository evidence not found.
- BIOS Settings: TBD - repository evidence not found.

## 5.3 Software Requirements

- Operating Systems: TBD - repository evidence not found.
- Middleware: TBD - repository evidence not found.
- Runtime Components: Python runtime (inferred from `.py` source files: `scripts/detect-impact.py`, `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`).
- Libraries: Python standard library `logging` module (detected import in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`).
- Drivers: TBD - repository evidence not found.
- Utilities: TBD - repository evidence not found.

## 5.4 Access & Permissions

| Role | Permissions | Notes |
|----------|----------|----------|
| Automation Executor | Execute `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline` (src/automation.py) | Inferred from function signatures; no RBAC model detected in repository |
| Deployment Executor | Execute `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` (src/deploy.py) | Inferred from function signatures |
| Backup Operator | Execute `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report` (src/backup.py) | Inferred from function signatures |
| DR Operator | Execute `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report` (src/dr_platform.py) | Inferred from function signatures |
| Security/Vault Administrator | Execute `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy` (src/security_vault.py) | Inferred from function signatures |
| Service Broker Administrator | Execute `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription` (src/service_broker.py) | Inferred from function signatures |

## 5.5 Security Requirements

- Security Baselines: `deploy_configuration_baseline` in `src/automation.py` applies "standard platform configuration baselines" (per module docstring/summary).
- Encryption Requirements: Managed via `src/security_vault.py` functions `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`.
- Compliance Requirements: TBD - repository evidence not found.
- Hardening Standards: TBD - repository evidence not found.

## 5.6 Secrets & Credential Dependencies

| Credential Type | Purpose | Storage Location |
|----------|----------|----------|
| Vault Namespace Credentials | Used by `create_vault_namespace` (src/security_vault.py) to establish secure namespace | TBD - repository evidence not found. |
| Customer-Managed Encryption Key | Created by `create_customer_managed_key`, rotated by `rotate_encryption_key`, assigned via `assign_key_to_service` (src/security_vault.py) | TBD - repository evidence not found. |
| Platform API Credentials | Used by `register_platform_api` (src/service_broker.py) | TBD - repository evidence not found. |

## 5.7 Certificate Requirements

| Certificate | Purpose | Owner |
|----------|----------|----------|
| TBD - repository evidence not found. | TBD - repository evidence not found. | TBD - repository evidence not found. |

## 5.8 Firewall & Network Dependencies

- Firewall Rules: TBD - repository evidence not found.
- Proxy Requirements: TBD - repository evidence not found.
- Load Balancer Dependencies: TBD - repository evidence not found.
- Required Ports: TBD - repository evidence not found.
- External Endpoints: TBD - repository evidence not found.

## 5.9 External Dependencies

- Active Directory: TBD - repository evidence not found.
- LDAP: TBD - repository evidence not found.
- DNS: TBD - repository evidence not found.
- Monitoring Platform: Referenced via `observability` domain support across `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`; underlying technology per catalog: Aria Operations, Aria Logs, Aria Network Insight.
- Backup Platform: Referenced via `src/backup.py`; underlying technology per catalog: Canopy Enterprise Backup, Avamar, Data Domain.
- Vault Solution: Referenced via `src/security_vault.py`; underlying technology per catalog: HashiCorp Vault.
- External APIs: Referenced via `src/service_broker.py` (`register_platform_api`, `validate_api_subscription`).
- Database Platforms: TBD - repository evidence not found.
- Message Queues: TBD - repository evidence not found.

## 5.10 Licensing Requirements

- Product Licenses: TBD - repository evidence not found.
- Subscription Entitlements: TBD - repository evidence not found.
- License Keys: TBD - repository evidence not found.

## 5.11 Skills Required

| Skill | Level |
|----------|----------|
| Python scripting | Intermediate (required to operate/extend `src/*.py` modules) |
| VMware Cloud Foundation / vSphere / NSX-T | Intermediate–Advanced (underlying platform technologies per catalog) |
| HashiCorp Vault administration | Intermediate (for `src/security_vault.py` operations) |
| Backup/DR operations (Avamar, Data Domain, SRM) | Intermediate (for `src/backup.py` and `src/dr_platform.py` operations) |

---

# 6. Input Parameters

| Parameter | Value | Description |
|----------|----------|----------|
| `environment_name` | TBD - environment-specific | Input to `provision_infrastructure`, `deploy_configuration_baseline` (src/automation.py) |
| `workflow_name` | TBD - workflow-specific | Input to `execute_platform_workflow`, `validate_automation_results` (src/automation.py) |
| `workload_name` | TBD - workload-specific | Input to `schedule_backup_job`, `execute_backup` (src/backup.py) |
| `backup_id` | TBD - backup-run-specific | Input to `validate_backup_integrity` (src/backup.py) |
| `region` | TBD - region-specific | Input to `deploy_network_foundation` (src/deploy.py) |
| `cluster_name` | TBD - cluster-specific | Input to `deploy_kubernetes_platform` (src/deploy.py) |
| `environment` | TBD - environment-specific | Input to `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability` (src/deploy.py) |
| `application_name` | TBD - application-specific | Input to `create_recovery_plan`, `validate_recovery_objectives` (src/dr_platform.py) |
| `target_site` | TBD - site-specific | Input to `execute_site_failover` (src/dr_platform.py) |
| `namespace_name` | TBD - namespace-specific | Input to `create_vault_namespace` (src/security_vault.py) |
| `key_name` | TBD - key-specific | Input to `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service` (src/security_vault.py) |
| `service_name` | TBD - service-specific | Input to `assign_key_to_service` (src/security_vault.py) |
| `policy_name` | TBD - policy-specific | Input to `validate_vault_policy` (src/security_vault.py) |
| `catalog_name` | TBD - catalog-specific | Input to `publish_service_catalog` (src/service_broker.py) |
| `api_name` | TBD - api-specific | Input to `register_platform_api` (src/service_broker.py) |
| `subscription_id` | TBD - subscription-specific | Input to `validate_api_subscription` (src/service_broker.py) |

---

# 7. Build Overview

## 7.1 Deployment Flow

```text
Prepare → Install → Configure → Validate → Handover
```

The repository-derived `deployment_flow` provides the authoritative build sequencing evidence, executed as follows:

```text
[Provision]
 provision_infrastructure (src/automation.py)
        │
        ▼
[Deploy - Baseline]
 deploy_configuration_baseline (src/automation.py)
        │
        ▼
[Validate - Automation]
 validate_automation_results (src/automation.py)
        │
        ▼
[Backup]
 schedule_backup_job → execute_backup → validate_backup_integrity → generate_backup_report
 (src/backup.py)
        │
        ▼
[Deploy - Platform]
 deploy_network_foundation → deploy_kubernetes_platform → deploy_ai_platform → deploy_data_platform
 (src/deploy.py)
        │
        ▼
[Validate - Observability]
 validate_platform_observability (src/deploy.py)
        │
        ▼
[Disaster Recovery]
 create_recovery_plan → validate_recovery_objectives
 (src/dr_platform.py)
        │
        ▼
[Validate - Security]
 validate_vault_policy (src/security_vault.py)
        │
        ▼
[Publish / Register]
 publish_service_catalog → register_platform_api
 (src/service_broker.py)
        │
        ▼
[Validate - API]
 validate_api_subscription (src/service_broker.py)
```

## 7.2 Build Phases

- Preparation: Access, secrets, and vault setup (Section 5) prior to `provision_infrastructure`.
- Installation: Execution of `provision_infrastructure`, `deploy_configuration_baseline` (src/automation.py) and `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` (src/deploy.py).
- Configuration: Application of security/vault configuration (`src/security_vault.py`) and service broker configuration (`src/service_broker.py`).
- Integration: Backup integration (`src/backup.py`) and disaster recovery integration (`src/dr_platform.py`).
- Validation: Execution of all `validate_*` functions per deployment_flow (Section 9.3).

---

# 8. Installation Procedure

## 8.1 Installation Overview

Installation is **automated**, driven by the sequential invocation of Python functions across the automation modules identified in the repository. No manual GUI-based installation steps are present in the scanned repository; all installation logic is expressed as discrete Python functions within `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py`.

## 8.2 Step-by-Step Installation

| Step | Action | Estimated Duration | Notes |
|----------|----------|----------|----------|
| 1 | Execute `provision_infrastructure(environment_name)` in `src/automation.py` | TBD - repository evidence not found. | First step in deployment_flow; provisions underlying infrastructure |
| 2 | Execute `deploy_configuration_baseline(environment_name)` in `src/automation.py` | TBD - repository evidence not found. | Applies standard platform configuration baseline |
| 3 | Execute `validate_automation_results(workflow_name)` in `src/automation.py` | TBD - repository evidence not found. | Validates automation execution outcome |
| 4 | Execute `schedule_backup_job(workload_name)` in `src/backup.py` | TBD - repository evidence not found. | Schedules backup job for workload |
| 5 | Execute `execute_backup(workload_name)` in `src/backup.py` | TBD - repository evidence not found. | Executes backup job |
| 6 | Execute `validate_backup_integrity(backup_id)` in `src/backup.py` | TBD - repository evidence not found. | Validates backup integrity |
| 7 | Execute `generate_backup_report()` in `src/backup.py` | TBD - repository evidence not found. | Generates backup report |
| 8 | Execute `deploy_network_foundation(region)` in `src/deploy.py` | TBD - repository evidence not found. | Deploys core networking components |
| 9 | Execute `deploy_kubernetes_platform(cluster_name)` in `src/deploy.py` | TBD - repository evidence not found. | Deploys Kubernetes platform services |
| 10 | Execute `deploy_ai_platform(environment)` in `src/deploy.py` | TBD - repository evidence not found. | Deploys AI platform services and model hosting infrastructure |
| 11 | Execute `deploy_data_platform(environment)` in `src/deploy.py` | TBD - repository evidence not found. | Deploys enterprise data services and analytics platform |
| 12 | Execute `validate_platform_observability(environment)` in `src/deploy.py` | TBD - repository evidence not found. | Validates monitoring, logging and observability configuration |
| 13 | Execute `create_recovery_plan(application_name)` in `src/dr_platform.py` | TBD - repository evidence not found. | Creates DR recovery plan |
| 14 | Execute `validate_recovery_objectives(application_name)` in `src/dr_platform.py` | TBD - repository evidence not found. | Validates recovery objectives |
| 15 | Execute `validate_vault_policy(policy_name)` in `src/security_vault.py` | TBD - repository evidence not found. | Validates vault security policy assignment |
| 16 | Execute `publish_service_catalog(catalog_name)` in `src/service_broker.py` | TBD - repository evidence not found. | Publishes cloud service catalog |
| 17 | Execute `register_platform_api(api_name)` in `src/service_broker.py` | TBD - repository evidence not found. | Registers platform API endpoint |
| 18 | Execute `validate_api_subscription(subscription_id)` in `src/service_broker.py` | TBD - repository evidence not found. | Validates API consumer subscriptions |

## 8.3 Platform-Specific Steps

Underlying platform technologies (VMware vSphere, NSX-T, Tanzu Kubernetes Grid, Aria Suite) are referenced in the Product Technology Catalog but no platform-specific installation scripts (e.g., OVA deployment, SDDC Manager bring-up scripts) were found in the scanned repository files. Refer to vendor documentation for platform-specific installation of underlying technologies.

TBD - repository evidence not found for platform-specific installation commands beyond the Python function calls listed in Section 8.2.

---

# 9. Deployment Procedure

## 9.1 Deployment Overview

Deployment follows the `deployment_flow` sequence documented in Section 7.1, executed via the automation and deployment modules identified in the repository. The sequence proceeds from infrastructure provisioning through configuration baseline, backup validation, platform deployment (network → Kubernetes → AI → data), observability validation, disaster recovery planning, vault policy validation, and service catalog/API publication.

## 9.2 Deployment Steps

- Provisioning: `provision_infrastructure` (src/automation.py)
- Installation: `deploy_configuration_baseline` (src/automation.py); `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` (src/deploy.py)
- Configuration: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service` (src/security_vault.py); `create_service_offering` (src/service_broker.py)
- Validation: `validate_automation_results` (src/automation.py); `validate_backup_integrity` (src/backup.py); `validate_platform_observability` (src/deploy.py); `validate_recovery_objectives` (src/dr_platform.py); `validate_vault_policy` (src/security_vault.py); `validate_api_subscription` (src/service_broker.py)

## 9.3 Validation Plan

### Health Checks

- Service Status Validation: `validate_automation_results(workflow_name)` in `src/automation.py`
- Component Health Validation: `validate_platform_observability(environment)` in `src/deploy.py`

### Connectivity Tests

- Network Validation: Confirmed indirectly through successful execution of `deploy_network_foundation(region)` in `src/deploy.py`
- External Dependency Validation: `validate_api_subscription(subscription_id)` in `src/service_broker.py`

### Functional Validation

- Core Function Verification: `validate_backup_integrity(backup_id)` in `src/backup.py`; `validate_recovery_objectives(application_name)` in `src/dr_platform.py`
- Integration Testing: `validate_vault_policy(policy_name)` in `src/security_vault.py`
- User Acceptance Testing: TBD - repository evidence not found.

## 9.4 Acceptance Criteria

The deployment is considered successful when:

- `validate_automation_results` returns success (src/automation.py)
- `validate_backup_integrity` returns success (src/backup.py)
- `validate_platform_observability` returns success (src/deploy.py)
- `validate_recovery_objectives` returns success (src/dr_platform.py)
- `validate_vault_policy` returns success (src/security_vault.py)
- `validate_api_subscription` returns success (src/service_broker.py)
- Customer acceptance completed (Section 15.3)

---

# 10. Configuration Steps

## 10.1 System Configuration

- Operating System: TBD - repository evidence not found.
- Network Settings: Configured via `deploy_network_foundation(region)` in `src/deploy.py`.
- Storage Configuration: Configured via `src/backup.py` (storage domain support) and underlying vSAN technology per catalog.

## 10.2 Security Configuration

- RBAC: TBD - repository evidence not found. (No RBAC configuration file detected.)
- IAM: TBD - repository evidence not found.
- Certificates: TBD - repository evidence not found.
- Hardening: `deploy_configuration_baseline(environment_name)` in `src/automation.py` applies standard platform configuration baselines.
- Audit Configuration: Observability/security domain support present in `src/automation.py`, `src/security_vault.py` but no explicit audit configuration function detected.

## 10.3 Integration Configuration

- APIs: `register_platform_api(api_name)` in `src/service_broker.py`
- External Systems: Vault integration via `create_vault_namespace`, `assign_key_to_service` (src/security_vault.py)
- Monitoring Platforms: Observability validated via `validate_platform_observability` (src/deploy.py); underlying technology per catalog: Aria Operations, Aria Logs
- Backup Platforms: `schedule_backup_job`, `execute_backup` (src/backup.py); underlying technology per catalog: Canopy Enterprise Backup, Avamar, Data Domain

---

# 11. Post-Installation Tasks

- Monitoring Configuration: Confirmed via `validate_platform_observability(environment)` in `src/deploy.py`.
- Backup Configuration: Confirmed via `schedule_backup_job`, `execute_backup`, `generate_backup_report` in `src/backup.py`.
- Documentation Updates: Update related documents listed in Section 2.4.
- CMDB Updates: TBD - repository evidence not found.
- Operations Handover: Refer to Section 15.

---

# 12. Troubleshooting

| Issue | Cause | Resolution |
|----------|----------|----------|
| `validate_automation_results` returns failure | Workflow execution error in `execute_platform_workflow` (src/automation.py) | Re-run `provision_infrastructure` and `deploy_configuration_baseline`; review automation module logs (`logging` import in src/automation.py) |
| `validate_backup_integrity` returns failure | Backup job did not complete successfully in `execute_backup` (src/backup.py) | Re-run `schedule_backup_job` and `execute_backup`; review `generate_backup_report` output |
| `validate_platform_observability` returns failure | Network/Kubernetes/AI/data platform deployment incomplete in `src/deploy.py` | Re-verify `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` execution order |
| `validate_recovery_objectives` returns failure | Recovery plan not properly created via `create_recovery_plan` (src/dr_platform.py) | Re-run `create_recovery_plan`; review DR readiness via `generate_dr_readiness_report` |
| `validate_vault_policy` returns failure | Vault namespace or key not correctly configured in `src/security_vault.py` | Verify `create_vault_namespace`, `create_customer_managed_key`, `assign_key_to_service` executed successfully |
| `validate_api_subscription` returns failure | Service catalog or API not correctly published/registered in `src/service_broker.py` | Re-run `publish_service_catalog` and `register_platform_api` |

---

# 13. Rollback Procedure

## 13.1 Conditions

- Failure Scenarios: Any `validate_*` function (Section 9.3) returning failure per `deployment_flow`.
- Rollback Triggers: Failure of `validate_automation_results`, `validate_backup_integrity`, `validate_platform_observability`, `validate_recovery_objectives`, `validate_vault_policy`, or `validate_api_subscription`.

## 13.2 Steps

Rollback dependencies are derived from `module_relationships`, reversing the domain dependency chain:

- Backup Restoration: Roll back `src/backup.py` operations (`execute_backup`, `schedule_backup_job`) before rolling back dependent `storage`, `observability`, and `security` domain configuration.
- Configuration Reversal: Reverse `deploy_configuration_baseline` (src/automation.py) before reversing `provision_infrastructure`, since `deploy_configuration_baseline` depends on infrastructure provisioned by `provision_infrastructure` (automation, lifecycle-management, observability, security domains).
- Platform Reversal: Reverse platform deployment in inverse order of `src/deploy.py` deployment_flow: `deploy_data_platform` → `deploy_ai_platform` → `deploy_kubernetes_platform` → `deploy_network_foundation` (each shares `lifecycle-management`, `observability`, `security` domain dependency per module_relationships).
- Security/Vault Reversal: Reverse `assign_key_to_service`, `rotate_encryption_key`, `create_customer_managed_key`, `create_vault_namespace` (src/security_vault.py) — these support `api-service-broker`, `automation`, `kubernetes`, `lifecycle-management`, `observability`, `security` domains, requiring service broker and Kubernetes-dependent components to be rolled back first.
- Service Broker Reversal: Reverse `register_platform_api`, `publish_service_catalog` (src/service_broker.py) before disabling vault-based key assignments, since `src/service_broker.py` shares `api-service-broker`, `lifecycle-management`, `observability`, `security` domains with `src/security_vault.py`.
- DR Reversal: Reverse `create_recovery_plan` (src/dr_platform.py) — shares `ai-platform`, `backup`, `lifecycle-management`, `observability`, `security` domains, requiring backup rollback to precede DR plan rollback.
- Validation Activities: Re-run applicable `validate_*` functions after each rollback stage to confirm reversal success.

---

# 14. Known Issues

```text
No known issues at the time of publication.
```

---

# 15. Handover and Acceptance

## 15.1 Handover Artifacts

- Configuration Backup: Output of `deploy_configuration_baseline` (src/automation.py)
- Deployment Logs: Generated via `logging` import used in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`
- Validation Results: Outputs of `validate_automation_results`, `validate_backup_integrity`, `validate_platform_observability`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`
- Runbooks: TBD - repository evidence not found.
- Related Documentation: Section 2.4 references

## 15.2 Ownership Transfer

- Operations Team: TBD - repository evidence not found.
- Support Team: TBD - repository evidence not found.
- Service Owner: TBD - repository evidence not found.

## 15.3 Acceptance Sign-Off

| Role | Name | Date | Status |
|----------|----------|----------|----------|
| Deployment Lead | TBD - repository evidence not found. | TBD - repository evidence not found. | Pending |
| Service Owner | TBD - repository evidence not found. | TBD - repository evidence not found. | Pending |
| Operations | TBD - repository evidence not found. | TBD - repository evidence not found. | Pending |

### 15.4 Operations Readiness

| Item | Status |
|--------|--------|
| OPG Completed | TBD - repository evidence not found. |
| Monitoring Configured | Confirmed via `validate_platform_observability` (src/deploy.py) |
| Alerting Configured | TBD - repository evidence not found. |
| Backup Configured | Confirmed via `schedule_backup_job`/`execute_backup` (src/backup.py) |
| Recovery Tested | Confirmed via `validate_recovery_objectives` (src/dr_platform.py) |
| Runbooks Delivered | TBD - repository evidence not found. |
| Ownership Assigned | TBD - repository evidence not found. |
| Escalation Process Defined | TBD - repository evidence not found. |

---

# 16. Appendices

## 16.1 Ports & Protocols

| Source | Destination | Port | Protocol | Purpose |
|----------|----------|----------|----------|----------|
| TBD - repository evidence not found. | TBD - repository evidence not found. | TBD - repository evidence not found. | TBD - repository evidence not found. | TBD - repository evidence not found. |

## 16.2 Network Plan

TBD - repository evidence not found. (No VLAN, subnet, routing, or network diagram artifacts detected in scanned repository; `deploy_network_foundation(region)` in `src/deploy.py` is the only network-related function detected.)

## 16.3 Naming Standards

| Object Type | Naming Convention |
|----------|----------|
| Server | TBD - repository evidence not found. |
| Database | TBD - repository evidence not found. |
| Network | TBD - repository evidence not found. |

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
| DR | Disaster Recovery (implemented via `src/dr_platform.py`) |
| SDDC | Software-Defined Data Center |
| VCS | VMware Cloud Services (product terminology per capability catalog: multi-tenancy) |

---

## Build Dependency Matrix

Derived from `module_relationships`, `function_relationships`, and `deployment_flow`.

| Module | Domains Supported | Depends On (Prior Stage) | Deployment Flow Functions | Validation Function(s) |
|---|---|---|---|---|
| `src/automation.py` | automation, lifecycle-management, observability, security | None (first stage) | `provision_infrastructure`, `deploy_configuration_baseline` | `validate_automation_results` |
| `src/backup.py` | backup, lifecycle-management, observability, security, storage | `src/automation.py` (shares lifecycle-management, observability, security) | `schedule_backup_job`, `execute_backup`, `generate_backup_report` | `validate_backup_integrity` |
| `src/deploy.py` | ai-platform, api-service-broker, compute, data-platform, kubernetes, lifecycle-management, networking, observability, security | `src/automation.py`, `src/backup.py` (shares lifecycle-management, observability, security) | `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` | `validate_platform_observability` |
| `src/dr_platform.py` | ai-platform, backup, disaster-recovery, lifecycle-management, observability, security | `src/backup.py`, `src/deploy.py` (shares ai-platform, backup, lifecycle-management, observability, security) | `create_recovery_plan`, `execute_site_failover` | `validate_recovery_objectives`, `generate_dr_readiness_report` |
| `src/security_vault.py` | api-service-broker, automation, kubernetes, lifecycle-management, observability, security | `src/automation.py`, `src/deploy.py` (shares automation, kubernetes, lifecycle-management, observability, security) | `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service` | `validate_vault_policy` |
| `src/service_broker.py` | api-service-broker, lifecycle-management, observability, security | `src/deploy.py`, `src/security_vault.py` (shares api-service-broker, lifecycle-management, observability, security) | `publish_service_catalog`, `register_platform_api`, `create_service_offering` | `validate_api_subscription` |
| `scripts/detect-impact.py` | ai-platform, api-service-broker, automation, compute, data-platform, lifecycle-management | Cross-cutting utility; not part of deployment_flow sequence | `resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request` | None detected |
