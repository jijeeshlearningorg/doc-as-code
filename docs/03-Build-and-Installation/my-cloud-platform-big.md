# Build & Installation Guide (BIG): My Cloud Platform (My Cloud Services)

**Author:** Platform Engineering Architecture Team
**Date:** Generated from repository analysis
**Version:** 1.0
**Status:** Draft
**Owner:** Platform Engineering

---

# 1. Document Control

## 1.1 Review & Approval

| Role | Name | Status |
|--------|--------|--------|
| Reviewer | Platform Engineering Lead | Pending |
| Security Review | Security Architecture Team | Pending |
| Document Owner | Jijeesh Valappil | Pending |

## 1.2 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | Generated | Initial Build & Installation Guide generated from `jijeeshlearningorg/greenfield-code` (branch `main`) repository scan | Platform Engineering Architecture Team |

---

# 2. Introduction

## 2.1 Purpose

This document defines the Build & Installation procedure for **My Cloud Platform (My Cloud Services)** — a VMware SDDC-based private/hybrid cloud platform. It covers installation, configuration, validation, rollback and operational handover of the platform components, based on the automation modules and deployment scripts present in the `greenfield-code` repository (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`, and `scripts/detect-impact.py`).

## 2.2 Audience

- Platform Engineers
- Cloud Infrastructure Automation Teams
- Security and Vault Operations Teams
- Disaster Recovery / Backup Operations Teams
- Operations and Support Teams

## 2.3 Scope

### In Scope

- Installation of the SDDC/VCS platform stack (compute, storage, networking, automation, containers)
- Configuration of automation, security vault, backup, DR and service broker modules
- Validation of deployment via repository-defined validation functions
- Rollback and operational handover procedures

### Out of Scope

- High-Level Design (HLD)
- Low-Level Design (LLD)
- Operational Procedures (OPG)

## 2.4 Related Documents

| Document Type | Document ID | Relationship |
|----------|----------|----------|
| HLD | TBD | Architecture Design |
| LLD | TBD | Detailed Design |
| BIG | This Document | Current Document |
| OPG | TBD | Operations Guide |
| ADR | TBD | Architecture Decisions |
| Runbooks | TBD | Operational Procedures |
| Vendor Documentation | VMware vSphere/NSX-T/Aria Suite/Tanzu Docs | Product Reference |

---

# 3. Deployment Context

- System Type: VMware SDDC-based Cloud Platform ("My Cloud Services")
- Deployment Model: Hybrid — on-premises private cloud with public-cloud (VMC) integration
- Platform/Provider: VMware vSphere/vSAN/NSX-T stack, Aria Suite, Tanzu Kubernetes Grid, SDDC Manager
- Environment: Greenfield deployment (repository: `jijeeshlearningorg/greenfield-code`, branch `main`)

---

# 4. Package / Build Description

## 4.1 Package Overview

The repository implements the automation and orchestration layer for **My Cloud Services**, comprising six Python automation modules and one CI/CD impact-detection script:

- `src/automation.py` — infrastructure provisioning and platform workflow orchestration
- `src/deploy.py` — network, Kubernetes, AI platform and data platform deployment
- `src/backup.py` — backup scheduling, execution and integrity validation
- `src/dr_platform.py` — disaster recovery planning, failover and readiness reporting
- `src/security_vault.py` — vault namespace and customer-managed key lifecycle
- `src/service_broker.py` — service catalog publishing and API registration
- `scripts/detect-impact.py` — CI/CD change-impact detection mapping changed files to product capabilities

These modules collectively deliver the domains: `ai-platform`, `api-service-broker`, `automation`, `backup`, `compute`, `data-platform`, `disaster-recovery`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security`, `storage`.

## 4.2 Product / Platform Components

| Component | Source / Location |
|----------|----------|
| vSphere / ESXi / vCenter | Core compute virtualization platform |
| vSAN | Software-defined storage |
| NSX-T | Software-defined networking and security |
| SDDC Manager | VMware Cloud Foundation lifecycle automation |
| vSphere Lifecycle Manager (vLCM) | Host and cluster lifecycle patching |
| Aria Automation / Aria Orchestrator | Provisioning and workflow automation — invoked by `src/automation.py` (`provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`) |
| Aria Operations / Aria Logs / Aria Network Insight | Observability — validated via `validate_automation_results`, `validate_platform_observability` |
| Aria Suite Lifecycle Manager | Aria Suite lifecycle management |
| Tanzu Kubernetes Grid / Tanzu Mission Control | Kubernetes platform — deployed via `deploy_kubernetes_platform` in `src/deploy.py` |
| HashiCorp Vault | Secrets and customer-managed key management — implemented in `src/security_vault.py` |
| Canopy Enterprise Backup / Avamar / Data Domain | Backup services — implemented in `src/backup.py` |
| SRM / vSphere Replication | Disaster recovery — implemented in `src/dr_platform.py` |
| HCX / VMC | Public cloud/workload mobility integration |
| Service Broker | Service catalog and API exposure — implemented in `src/service_broker.py` |
| Trend Micro / Nessus | Endpoint protection and vulnerability scanning |
| CI/CD Impact Detection | `scripts/detect-impact.py` — maps changed files to affected capabilities for documentation/pipeline automation |

## 4.3 Versioning

| Component | Version |
|----------|----------|
| Repository | `jijeeshlearningorg/greenfield-code` @ `main` |
| Automation modules | As committed on `main` (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) |
| Impact detection script | `scripts/detect-impact.py` (351 lines) |
| VMware component versions | To be recorded per environment build (inferred — not present in repository) |

## 4.4 Installation Notes

- Deployment is automation-driven through Python modules; no infrastructure-as-code manifests (e.g., Terraform/Ansible) were detected in the scanned repository — orchestration logic resides solely in `src/*.py`.
- `scripts/detect-impact.py` is a CI/CD utility that determines which product capabilities are impacted by a pull request, based on changed file paths — it does not perform platform deployment itself, but supports change governance for the pipeline.
- All modules use `logging` (see Module Relationships) for operational traceability; no external configuration files were detected in the repository — configuration values (environment names, workflow names, cluster names, key names) are passed as function arguments at call time.
- No classes were detected (0 classes); all logic is implemented as module-level functions.

---

# 5. Pre-Requisites

## 5.1 Infrastructure

- Compute: ESXi hosts sized for vSphere cluster hosting (compute domain)
- Storage: vSAN datastore or Fibre Channel storage backing the storage domain
- Network: NSX-T overlay/underlay segments, transport zones, edge clusters (`deploy_network_foundation` in `src/deploy.py`)
- DNS: Resolvable forward/reverse records for all management and workload components
- NTP: Time synchronization across vCenter, ESXi, NSX-T, Aria Suite, Tanzu
- Backup Infrastructure: Canopy Enterprise Backup / Avamar / Data Domain targets for `src/backup.py` operations

## 5.2 Hardware Requirements

- CPU: Per VMware SDDC sizing guidance for target workload domains
- Memory: Per VMware SDDC sizing guidance
- Storage: vSAN-eligible disk groups / Fibre Channel LUNs
- Rack Requirements: Per data center standards
- BIOS Settings: Virtualization extensions (VT-x/AMD-V) enabled

## 5.3 Software Requirements

- Operating Systems: ESXi hypervisor images
- Middleware: Aria Suite Lifecycle Manager, SDDC Manager
- Runtime Components: Tanzu Kubernetes Grid runtime, Python runtime for automation modules (`src/*.py`)
- Libraries: Python standard `logging` module (imported dependency across modules)
- Drivers: ESXi certified I/O and storage drivers
- Utilities: `scripts/detect-impact.py` requires a Python interpreter and YAML parsing capability (`read_yaml`)

## 5.4 Access & Permissions

| Role | Permissions | Notes |
|----------|----------|----------|
| Platform Administrator | Full vCenter/NSX-T/SDDC Manager admin | Executes `provision_infrastructure`, `deploy_network_foundation` |
| Automation Operator | Aria Automation workflow execution rights | Executes `execute_platform_workflow`, `deploy_configuration_baseline` |
| Security/Vault Administrator | Vault namespace and key management rights | Executes `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key` |
| Backup Operator | Backup platform scheduling and execution rights | Executes `schedule_backup_job`, `execute_backup` |
| DR Operator | Site Recovery Manager / replication rights | Executes `create_recovery_plan`, `execute_site_failover` |
| Service Broker Administrator | Catalog and API publishing rights | Executes `publish_service_catalog`, `register_platform_api` |
| CI/CD Pipeline Service Account | Repository read access | Executes `scripts/detect-impact.py` |

## 5.5 Security Requirements

- Security Baselines: Platform hardening per VMware SDDC security guidelines
- Encryption Requirements: Customer-managed keys via `src/security_vault.py` (`create_customer_managed_key`, `rotate_encryption_key`)
- Compliance Requirements: Vault policy validation via `validate_vault_policy`
- Hardening Standards: Trend Micro endpoint protection, Nessus vulnerability scanning integration

## 5.6 Secrets & Credential Dependencies

| Credential Type | Purpose | Storage Location |
|----------|----------|----------|
| Vault Administrative Credentials | Vault namespace and key operations (`src/security_vault.py`) | HashiCorp Vault (enterprise secrets management) |
| Automation Service Account Credentials | Aria Automation workflow execution (`src/automation.py`) | Vault-managed secret store |
| Backup Platform Credentials | Backup job scheduling/execution (`src/backup.py`) | Vault-managed secret store |
| DR Platform Credentials | Site failover execution (`src/dr_platform.py`) | Vault-managed secret store |
| API Subscription Keys | Service Broker API validation (`src/service_broker.py`) | Vault-managed secret store |

## 5.7 Certificate Requirements

| Certificate | Purpose | Owner |
|----------|----------|----------|
| vCenter/NSX-T Solution Certificates | Component trust and mutual TLS | Platform Engineering |
| Aria Suite Certificates | Service-to-service trust | Platform Engineering |
| Vault TLS Certificates | Secure vault namespace communication (`create_vault_namespace`) | Security Team |
| Service Broker API Certificates | API endpoint TLS (`register_platform_api`) | Platform Engineering |

## 5.8 Firewall & Network Dependencies

- Firewall Rules: Management-plane to component connectivity (vCenter, NSX-T Manager, SDDC Manager, Aria Suite, Vault, Backup targets)
- Proxy Requirements: Outbound proxy for VMC/public cloud integration (if applicable)
- Load Balancer Dependencies: NSX-T load balancer or external LB for Aria Suite and Tanzu control plane endpoints
- Required Ports: To be finalized per component (vCenter 443, NSX-T Manager 443, Vault 8200, etc. — inferred)
- External Endpoints: VMC / hyperscaler connectivity endpoints for public-cloud integration domain

## 5.9 External Dependencies

- Active Directory / LDAP: Identity source for platform RBAC
- DNS: Name resolution for all components
- Monitoring Platform: Aria Operations, Aria Logs (`validate_platform_observability`, `validate_automation_results`)
- Backup Platform: Canopy Enterprise Backup, Avamar, Data Domain
- Vault Solution: HashiCorp Vault (`src/security_vault.py`)
- External APIs: Service Broker-registered platform APIs (`register_platform_api`)
- Database Platforms: Not explicitly detected in repository — to be confirmed at design stage
- Message Queues: Not explicitly detected in repository

## 5.10 Licensing Requirements

- VMware vSphere, vSAN, NSX-T licensing
- Aria Suite (Automation, Orchestrator, Operations, Logs, Network Insight) licensing
- Tanzu Kubernetes Grid / Tanzu Mission Control entitlements
- HashiCorp Vault Enterprise licensing
- Trend Micro and Nessus licensing
- Canopy Enterprise Backup / Avamar entitlements

## 5.11 Skills Required

| Skill | Level |
|----------|----------|
| VMware SDDC (vSphere, vSAN, NSX-T) | Expert |
| Aria Suite Automation/Orchestration | Advanced |
| Tanzu Kubernetes Grid Administration | Advanced |
| HashiCorp Vault Administration | Advanced |
| Python Scripting (automation modules) | Intermediate |
| Backup & DR Operations (Avamar, SRM) | Advanced |
| CI/CD Pipeline Administration | Intermediate |

---

# 6. Input Parameters

| Parameter | Value | Description |
|----------|----------|----------|
| `environment_name` | Environment-specific string | Passed to `provision_infrastructure`, `deploy_configuration_baseline` in `src/automation.py` |
| `workflow_name` | Automation workflow identifier | Passed to `execute_platform_workflow`, `validate_automation_results` |
| `workload_name` | Workload identifier | Passed to `schedule_backup_job`, `execute_backup` in `src/backup.py` |
| `backup_id` | Backup job identifier | Passed to `validate_backup_integrity` |
| `region` | Deployment region identifier | Passed to `deploy_network_foundation` in `src/deploy.py` |
| `cluster_name` | Kubernetes cluster identifier | Passed to `deploy_kubernetes_platform` |
| `environment` | Target environment | Passed to `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability` |
| `application_name` | Application identifier | Passed to `create_recovery_plan`, `validate_recovery_objectives` in `src/dr_platform.py` |
| `target_site` | DR target site identifier | Passed to `execute_site_failover` |
| `namespace_name` | Vault namespace identifier | Passed to `create_vault_namespace` in `src/security_vault.py` |
| `key_name` | Encryption key identifier | Passed to `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service` |
| `service_name` | Platform service identifier | Passed to `assign_key_to_service` |
| `policy_name` | Vault security policy identifier | Passed to `validate_vault_policy` |
| `catalog_name` | Service catalog identifier | Passed to `publish_service_catalog` in `src/service_broker.py` |
| `api_name` | Platform API identifier | Passed to `register_platform_api` |
| `subscription_id` | API subscriber identifier | Passed to `validate_api_subscription` |

---

# 7. Build Overview

## 7.1 Deployment Flow

```text
Prepare → Install → Configure → Validate → Handover
```

## 7.2 Build Phases

- Preparation: Infrastructure readiness, credentials, vault setup
- Installation: Network foundation, compute/storage, Kubernetes, AI/data platform deployment
- Configuration: Automation baselines, security keys, backup jobs, DR plans, service catalog
- Integration: Service broker API registration, observability integration
- Validation: Automated validation functions across all modules

---

# 8. Installation Procedure

## 8.1 Installation Overview

Installation is **automation-driven**, executed through the Python modules in `src/`. Each module exposes discrete functions invoked in sequence as part of the platform build pipeline. No manual GUI-only installation path is defined in the repository; all actions are represented as callable functions returning boolean or structured (`dict`) success indicators.

## 8.2 Step-by-Step Installation

| Step | Action | Estimated Duration | Notes |
|----------|----------|----------|----------|
| 1 | Run `provision_infrastructure(environment_name)` in `src/automation.py` to provision base infrastructure | Environment-dependent | Automates infrastructure provisioning |
| 2 | Run `deploy_network_foundation(region)` in `src/deploy.py` to deploy core networking (NSX-T) | Environment-dependent | Establishes networking domain foundation |
| 3 | Run `deploy_configuration_baseline(environment_name)` in `src/automation.py` to apply standard configuration baselines | Environment-dependent | Applies platform configuration baseline |
| 4 | Run `deploy_kubernetes_platform(cluster_name)` in `src/deploy.py` to deploy Tanzu Kubernetes Grid services | Environment-dependent | Deploys containers/kubernetes domain |
| 5 | Run `deploy_ai_platform(environment)` in `src/deploy.py` to deploy AI platform/model hosting infrastructure | Environment-dependent | Optional based on product offering |
| 6 | Run `deploy_data_platform(environment)` in `src/deploy.py` to deploy enterprise data services | Environment-dependent | Deploys data-platform domain |
| 7 | Run `create_vault_namespace(namespace_name)` in `src/security_vault.py` to establish secure vault namespace | Short | Security prerequisite for key management |
| 8 | Run `create_customer_managed_key(key_name)` and `assign_key_to_service(key_name, service_name)` | Short | Establishes encryption for platform services |
| 9 | Run `execute_platform_workflow(workflow_name)` in `src/automation.py` to execute platform orchestration workflows | Environment-dependent | Orchestration via Aria Automation |
| 10 | Run `schedule_backup_job(workload_name)` and `execute_backup(workload_name)` in `src/backup.py` | Short | Establishes backup domain coverage |
| 11 | Run `create_recovery_plan(application_name)` in `src/dr_platform.py` | Environment-dependent | Establishes DR readiness |
| 12 | Run `publish_service_catalog(catalog_name)` and `register_platform_api(api_name)` in `src/service_broker.py` | Short | Exposes platform services via API/service broker |

## 8.3 Platform-Specific Steps

- VMware: Provisioning and configuration through vCenter, NSX-T Manager, SDDC Manager as targeted by `provision_infrastructure` and `deploy_network_foundation`.
- Kubernetes: Tanzu Kubernetes Grid provisioning via `deploy_kubernetes_platform`.
- CI/CD Pipeline: `scripts/detect-impact.py` executes as part of the pull-request pipeline (`main` function) to detect capability impact of code changes and produce a JSON impact report (`write_json`, `build_doc_request`).

---

# 9. Deployment Procedure

## 9.1 Deployment Overview

Deployment follows a phased, function-driven strategy: infrastructure provisioning → network foundation → configuration baseline → workload platform (Kubernetes/AI/Data) → security/vault configuration → backup/DR configuration → service exposure. Each phase includes an explicit validation function invoked before proceeding to the next phase.

## 9.2 Deployment Steps

- Provisioning: `provision_infrastructure` (`src/automation.py`)
- Installation: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` (`src/deploy.py`)
- Configuration: `deploy_configuration_baseline` (`src/automation.py`), `create_vault_namespace`, `create_customer_managed_key`, `assign_key_to_service` (`src/security_vault.py`)
- Validation: `validate_automation_results`, `validate_platform_observability`, `validate_vault_policy`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_api_subscription`

## 9.3 Validation Plan

### Health Checks

- Service Status Validation: `validate_automation_results(workflow_name)` in `src/automation.py`
- Component Health Validation: `validate_platform_observability(environment)` in `src/deploy.py`

### Connectivity Tests

- Network Validation: Confirm NSX-T segments deployed by `deploy_network_foundation` are reachable
- External Dependency Validation: Confirm vault, backup, and monitoring endpoints are reachable prior to executing dependent functions

### Functional Validation

- Core Function Verification: `validate_backup_integrity(backup_id)` in `src/backup.py`, `validate_recovery_objectives(application_name)` in `src/dr_platform.py`
- Integration Testing: `validate_vault_policy(policy_name)` in `src/security_vault.py`
- User Acceptance Testing: `validate_api_subscription(subscription_id)` in `src/service_broker.py`

## 9.4 Acceptance Criteria

The deployment is considered successful when:

- `provision_infrastructure` and `deploy_configuration_baseline` return successful (`True`) status
- `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` complete successfully
- `validate_automation_results` and `validate_platform_observability` confirm operational health
- `validate_backup_integrity` and `validate_recovery_objectives` confirm backup/DR readiness
- `validate_vault_policy` confirms security policy compliance
- `validate_api_subscription` confirms service broker consumer readiness
- Customer/service owner acceptance completed

---

# 10. Configuration Steps

## 10.1 System Configuration

- Operating System: ESXi host configuration per SDDC standards
- Network Settings: NSX-T segments, transport zones configured via `deploy_network_foundation`
- Storage Configuration: vSAN datastore configuration supporting `storage` domain

## 10.2 Security Configuration

- RBAC: Role assignment per Section 5.4
- IAM: Vault namespace-based access via `create_vault_namespace`
- Certificates: TLS certificate issuance per Section 5.7
- Hardening: Trend Micro/Nessus baseline application
- Audit Configuration: Logging enabled across automation modules (`logging` import detected in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`)

## 10.3 Integration Configuration

- APIs: `register_platform_api(api_name)` and `create_service_offering(service_name)` in `src/service_broker.py`
- External Systems: Vault key assignment via `assign_key_to_service`
- Monitoring Platforms: Aria Operations/Logs integration validated by `validate_platform_observability`
- Backup Platforms: Canopy/Avamar integration via `schedule_backup_job`, `execute_backup`

---

# 11. Post-Installation Tasks

- Monitoring Configuration: Confirm Aria Operations/Aria Logs dashboards reflect deployed components (`validate_platform_observability`)
- Backup Configuration: Confirm scheduled jobs from `schedule_backup_job` are active and reporting via `generate_backup_report`
- Documentation Updates: Update architecture and configuration documentation to reflect deployed environment
- CMDB Updates: Register new environment/cluster/service entries
- Operations Handover: Execute Section 15 handover procedure

---

# 12. Troubleshooting

| Issue | Cause | Resolution |
|----------|----------|----------|
| `provision_infrastructure` returns `False` | Infrastructure prerequisites (compute/storage/network) not met | Verify Section 5.1–5.3 prerequisites and re-run provisioning |
| `execute_platform_workflow` fails | Aria Automation workflow misconfiguration or credential failure | Verify automation service account credentials (Section 5.6) and workflow definition |
| `validate_backup_integrity` returns `False` | Backup job corrupted or incomplete | Re-run `execute_backup(workload_name)` and re-validate |
| `execute_site_failover` fails | Target site not ready or replication lag | Verify `create_recovery_plan` was executed successfully and replication is current |
| `validate_vault_policy` returns `False` | Vault policy not assigned/misconfigured | Review vault namespace and policy configuration in `src/security_vault.py` workflow |
| `validate_api_subscription` fails | Subscription not registered or expired | Confirm `register_platform_api` and subscription registration in Service Broker |
| `scripts/detect-impact.py` produces empty impacted capabilities | Changed file paths do not match configured `path_mapping` | Review `resolve_capabilities_for_changed_file` mapping configuration |

---

# 13. Rollback Procedure

## 13.1 Conditions

- Failure Scenarios: Any validation function (`validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`) returns `False`
- Rollback Triggers: Failed deployment phase in Section 9.2, security policy non-compliance, or failed acceptance criteria (Section 9.4)

## 13.2 Steps

- Backup Restoration: Restore configuration baseline using last known-good backup validated by `validate_backup_integrity`
- Configuration Reversal: Revert configuration baseline applied by `deploy_configuration_baseline`; remove partially applied vault keys (`rotate_encryption_key` to previous key state) and service broker registrations (`register_platform_api`, `publish_service_catalog`)
- Validation Activities: Re-run applicable validation functions (`validate_automation_results`, `validate_platform_observability`, `validate_vault_policy`) to confirm the environment has returned to the last stable state

---

# 14. Known Issues

```text
No known issues at the time of publication.
```

---

# 15. Handover and Acceptance

## 15.1 Handover Artifacts

- Configuration Backup (baseline applied via `deploy_configuration_baseline`)
- Deployment Logs (generated via `logging` module across `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`)
- Validation Results (`validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`)
- Backup and DR Reports (`generate_backup_report`, `generate_dr_readiness_report`)
- Runbooks and Related Documentation

## 15.2 Ownership Transfer

- Operations Team: Ongoing monitoring, backup and DR operations
- Support Team: Incident and troubleshooting support (Section 12)
- Service Owner: Overall platform service accountability

## 15.3 Acceptance Sign-Off

| Role | Name | Date | Status |
|----------|----------|----------|----------|
| Deployment Lead | | | |
| Service Owner | | | |
| Operations | | | |

### 15.4 Operations Readiness

| Item | Status |
|--------|--------|
| OPG Completed | Pending |
| Monitoring Configured | Pending (`validate_platform_observability`) |
| Alerting Configured | Pending |
| Backup Configured | Pending (`schedule_backup_job`, `execute_backup`) |
| Recovery Tested | Pending (`validate_recovery_objectives`) |
| Runbooks Delivered | Pending |
| Ownership Assigned | Pending |
| Escalation Process Defined | Pending |

---

# 16. Appendices

## 16.1 Ports & Protocols

| Source | Destination | Port | Protocol | Purpose |
|----------|----------|----------|----------|----------|
| Automation Host | vCenter | 443 | HTTPS | Infrastructure provisioning (`provision_infrastructure`) |
| Automation Host | NSX-T Manager | 443 | HTTPS | Network foundation deployment (`deploy_network_foundation`) |
| Automation Host | HashiCorp Vault | 8200 | HTTPS | Vault namespace/key operations (`src/security_vault.py`) |
| Automation Host | Aria Automation | 443 | HTTPS | Workflow execution (`execute_platform_workflow`) |
| Automation Host | Backup Platform | 443 | HTTPS | Backup scheduling/execution (`src/backup.py`) |
| Automation Host | SRM/Replication | 443 | HTTPS | DR operations (`src/dr_platform.py`) |
| Automation Host | Service Broker | 443 | HTTPS | Catalog/API publishing (`src/service_broker.py`) |
| CI/CD Pipeline | Repository | 443 | HTTPS | Impact detection (`scripts/detect-impact.py`) |

*(Ports listed are industry-standard defaults — inferred, not explicitly present in repository; confirm per environment.)*

## 16.2 Network Plan

- VLANs: Per NSX-T segment design established by `deploy_network_foundation`
- Subnets: Per environment/region design
- Routing: NSX-T Tier-0/Tier-1 gateway design
- Network Diagrams: To be produced in HLD/LLD
- Firewall Zones: Management, workload, and DMZ zones per SDDC standard architecture

## 16.3 Naming Standards

| Object Type | Naming Convention |
|----------|----------|
| Server | To be defined per organizational standard |
| Database | To be defined per organizational standard |
| Network | To be defined per organizational standard (aligned to NSX-T segment naming) |

## 16.4 Glossary

| Term | Definition |
|----------|----------|
| API | Application Programming Interface |
| BIG | Build & Installation Guide |
| CI/CD | Continuous Integration / Continuous Delivery |
| DNS | Domain Name System |
| DR | Disaster Recovery |
| HLD | High-Level Design |
| IAM | Identity and Access Management |
| LLD | Low-Level Design |
| NSX-T | VMware software-defined networking and security platform |
| OPG | Operations Guide |
| PKI | Public Key Infrastructure |
| RBAC | Role-Based Access Control |
| SDDC | Software-Defined Data Center |
| VCS | (My) Cloud Services |
| vSAN | VMware software-defined storage platform |
