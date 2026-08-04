# Build & Installation Guide (BIG): my-cloud-platform

**Author:** Platform Engineering Architecture Team
**Date:** 2024
**Version:** 1.0
**Status:** Final
**Owner:** Platform Engineering / Cloud Infrastructure Services

---

# 1. Document Control

## 1.1 Review & Approval

| Role | Name | Status |
|--------|--------|--------|
| Reviewer | Platform Engineering Lead | Approved |
| Security Review | Security & Compliance Team | Approved |
| Document Owner | Cloud Platform Architecture Team | Approved |

## 1.2 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | 2024 | Initial publication of Build & Installation Guide for my-cloud-platform | Platform Engineering Architecture Team |

---

# 2. Introduction

## 2.1 Purpose

This document describes the end-to-end build, installation, configuration, validation, rollback and operational handover procedures for **my-cloud-platform**, a VMware-based private cloud platform (VCS) built on vSphere, vSAN and NSX-T, orchestrated through the VMware Aria Suite, and extended with Kubernetes (Tanzu), disaster recovery, backup, security/vault, and self-service API/service broker capabilities. It provides the authoritative build reference for engineers deploying the platform into a new environment or region.

## 2.2 Audience

- Platform Engineers
- Cloud Infrastructure Teams
- Automation/DevOps Engineers
- Security & Compliance Teams
- Operations Teams
- Support Teams

## 2.3 Scope

### In Scope

- Installation of core compute, storage and networking foundation (vSphere, vSAN, NSX-T)
- Deployment of automation, orchestration and lifecycle management components (Aria Automation, Aria Orchestrator, SDDC Manager, vLCM)
- Deployment of Kubernetes platform services (Tanzu Kubernetes Grid)
- Configuration of security, secrets and encryption key management (HashiCorp Vault integration)
- Configuration of backup services (Canopy Enterprise Backup, Avamar, Data Domain)
- Configuration of disaster recovery (SRM, vSphere Replication)
- Deployment of the service broker / API layer for self-service consumption
- Validation, rollback and operational handover procedures

### Out of Scope

- High-Level Design (HLD)
- Low-Level Design (LLD)
- Operational Procedures (OPG)

## 2.4 Related Documents

| Document Type | Document ID | Relationship |
|----------|----------|----------|
| HLD | HLD-MCP-001 | Architecture Design |
| LLD | LLD-MCP-001 | Detailed Design |
| BIG | BIG-MCP-001 | Current Document |
| OPG | OPG-MCP-001 | Operations Guide |
| ADR | ADR-MCP-Series | Architecture Decisions |
| Runbooks | RB-MCP-Series | Operational Procedures |
| Vendor Documentation | VMware, HashiCorp, Trend Micro, Dell EMC Product Docs | Product Reference |

---

# 3. Deployment Context

- System Type: Private/Hybrid Cloud Platform (Software-Defined Data Center)
- Deployment Model: On-premises VMware Cloud Foundation-aligned SDDC with hyperscaler (public cloud) extension via HCX/VMC
- Platform/Provider: VMware (vSphere, vSAN, NSX-T, Aria Suite, Tanzu) with public cloud integration
- Environment: Multi-tenant production cloud platform (`my-cloud-platform`), supporting Development, Staging and Production environments deployed via repeatable automation

---

# 4. Package / Build Description

## 4.1 Package Overview

`my-cloud-platform` is a modular, automation-driven cloud platform delivered as a set of infrastructure, automation and security modules. The build package consists of:

- CI/CD impact-detection tooling (`scripts/detect-impact.py`) that maps changed source paths to impacted platform capabilities for release governance
- Automation modules (`src/automation.py`) driving infrastructure provisioning, workflow execution and configuration baselining
- Deployment modules (`src/deploy.py`) provisioning network foundation, Kubernetes platform, AI platform, data platform and observability validation
- Backup modules (`src/backup.py`) for scheduling, executing and validating workload backups
- Disaster recovery modules (`src/dr_platform.py`) for recovery plan creation, site failover and RTO/RPO validation
- Security/vault modules (`src/security_vault.py`) for namespace isolation, customer-managed key lifecycle and policy validation
- Service broker modules (`src/service_broker.py`) exposing platform capabilities through a self-service API and catalog

## 4.2 Product / Platform Components

| Component | Source / Location |
|----------|----------|
| vCenter / ESXi (Compute) | VMware vSphere platform |
| vSAN (Storage) | VMware vSAN software-defined storage |
| NSX-T (Networking) | VMware NSX-T Data Center |
| SDDC Manager | VMware Cloud Foundation |
| vSphere Lifecycle Manager (vLCM) | VMware vSphere |
| Aria Automation | VMware Aria Suite |
| Aria Orchestrator | VMware Aria Suite |
| Aria Operations | VMware Aria Suite |
| Aria Logs | VMware Aria Suite |
| Aria Network Insight | VMware Aria Suite |
| Aria Suite Lifecycle Manager | VMware Aria Suite |
| Tanzu Kubernetes Grid | VMware Tanzu |
| Tanzu Mission Control | VMware Tanzu |
| HashiCorp Vault | Enterprise secrets/key management (`src/security_vault.py`) |
| Canopy Enterprise Backup | Enterprise backup platform (`src/backup.py`) |
| Avamar / Data Domain | Backup storage and recovery appliance |
| Site Recovery Manager (SRM) | Disaster recovery orchestration (`src/dr_platform.py`) |
| vSphere Replication | VM-level replication engine |
| HCX | Workload mobility / migration |
| VMware Cloud (VMC) | Public cloud integration |
| Service Broker | Self-service catalog and API layer (`src/service_broker.py`) |
| Trend Micro | Endpoint protection / anti-malware |
| Nessus | Vulnerability scanning |
| Automation Engine | `src/automation.py` |
| Deployment Engine | `src/deploy.py` |
| CI/CD Impact Detection | `scripts/detect-impact.py` |

## 4.3 Versioning

| Component | Version (Reference) |
|----------|----------|
| vSphere / ESXi | Latest supported LTS release per VMware Product Interoperability Matrix |
| vSAN | Aligned to vSphere version |
| NSX-T Data Center | Latest validated release |
| SDDC Manager | Latest VCF release supported by Product Interoperability Matrix |
| Aria Suite (Automation/Orchestrator/Operations/Logs/Network Insight) | Latest Aria Suite release compatible with vSphere/NSX-T |
| Tanzu Kubernetes Grid | Latest TKG release supported by vSphere |
| HashiCorp Vault | Latest Enterprise release |
| Canopy Enterprise Backup / Avamar / Data Domain | Vendor-supported release per backup platform compatibility matrix |
| SRM / vSphere Replication | Aligned to vSphere version |
| Repository build tooling | `main` branch, `jijeeshlearningorg/greenfield-code` |

Actual deployed versions must be recorded in the environment-specific Configuration Management Database (CMDB) entry at build completion.

## 4.4 Installation Notes

- All modules assume a functioning underlying vSphere/vSAN/NSX-T foundation prior to automation, backup, DR, security and service broker layers being deployed.
- Automation workflows (`src/automation.py`, `src/deploy.py`) are idempotent at the environment/workflow level; re-running against an already-provisioned environment should be validated in a non-production environment first.
- Public cloud integration (VMC, HCX) is optional and only required where `public-cloud-integration` capability is in scope for the target deployment.
- The `scripts/detect-impact.py` tool is a CI/CD governance utility; it does not provision infrastructure but determines which capability documentation/deployment pipelines are impacted by a given pull request and must be included in change review.
- Deployment order must follow: Networking Foundation → Compute/Storage → Automation/Orchestration → Security/Vault → Kubernetes/AI/Data Platforms → Backup → DR → Service Broker → Monitoring/Observability validation.

---

# 5. Pre-Requisites

## 5.1 Infrastructure

- Compute: ESXi host cluster(s) sized for management and workload domains
- Storage: vSAN-eligible local storage per host, or supported Fibre Channel storage array for optional external storage
- Network: NSX-T ready physical fabric (leaf/spine or equivalent), dedicated VLANs for management, vMotion, vSAN, NSX overlay/underlay, and edge uplinks
- DNS: Forward and reverse DNS resolution for all management components (vCenter, NSX Manager, SDDC Manager, Aria Suite nodes)
- NTP: Time synchronization source available to all ESXi hosts and management appliances
- Backup Infrastructure: Data Domain appliance and Avamar/Canopy Enterprise Backup connectivity

## 5.2 Hardware Requirements

- CPU: Server-class CPUs certified on VMware Hardware Compatibility List (HCL)
- Memory: Sized per management and workload domain design (minimum per Aria Suite/Tanzu sizing guides)
- Storage: vSAN-certified disks/controllers per HCL; Data Domain capacity sized to backup retention policy
- Rack Requirements: Standard 42U rack with redundant power feeds per site design
- BIOS Settings: Virtualization extensions (VT-x/AMD-V), IOMMU/VT-d enabled, power management set to OS-controlled per VMware best practice

## 5.3 Software Requirements

- Operating Systems: ESXi hypervisor images; Photon OS/Linux appliances for Aria Suite, NSX-T, SDDC Manager
- Middleware: Aria Suite Lifecycle Manager for appliance deployment orchestration
- Runtime Components: Python 3.x runtime for automation/deployment scripts (`scripts/detect-impact.py`, `src/*.py`)
- Libraries: PyYAML (or equivalent) for YAML parsing in impact-detection tooling
- Drivers: Certified I/O, storage and network drivers per VMware HCL
- Utilities: Standard CLI tooling (PowerCLI, govc, kubectl, vault CLI, terraform if applicable)

## 5.4 Access & Permissions

| Role | Permissions | Notes |
|----------|----------|----------|
| Platform Build Engineer | Administrator on vCenter, NSX-T Manager, SDDC Manager | Required for initial foundation build |
| Automation Service Account | API access to Aria Automation/Orchestrator | Used by `src/automation.py` workflows |
| Security/Vault Administrator | Admin on HashiCorp Vault namespace | Used by `src/security_vault.py` operations |
| Backup Administrator | Admin on Canopy/Avamar/Data Domain | Used by `src/backup.py` operations |
| DR Administrator | Admin on SRM/vSphere Replication | Used by `src/dr_platform.py` operations |
| Service Broker Administrator | Publish/manage catalog and API registrations | Used by `src/service_broker.py` operations |
| Read-Only Auditor | View-only across all consoles | For compliance/audit review |

## 5.5 Security Requirements

- Security Baselines: VMware vSphere Security Configuration Guide, CIS Benchmarks for ESXi/vCenter
- Encryption Requirements: Customer-managed encryption keys via HashiCorp Vault for vSAN encryption, VM encryption and backup encryption
- Compliance Requirements: Organizational compliance framework (e.g., ISO 27001/PCI-DSS as applicable) plus vulnerability scanning via Nessus
- Hardening Standards: STIG/CIS hardening applied to all management appliances and endpoint protection via Trend Micro

## 5.6 Secrets & Credential Dependencies

| Credential Type | Purpose | Storage Location |
|----------|----------|----------|
| vCenter/NSX-T/SDDC Manager admin credentials | Initial platform build and configuration | HashiCorp Vault |
| Aria Suite service account credentials | Automation and orchestration workflow execution | HashiCorp Vault |
| Customer-managed encryption keys | vSAN/VM/backup encryption (`create_customer_managed_key`) | HashiCorp Vault |
| Backup platform service credentials | Backup scheduling and execution (`schedule_backup_job`, `execute_backup`) | HashiCorp Vault |
| DR platform service credentials | Recovery plan and failover execution | HashiCorp Vault |
| Service Broker API credentials | API registration and subscription validation | HashiCorp Vault |
| CI/CD pipeline tokens | Repository access for `detect-impact.py` execution | CI/CD secrets store |

## 5.7 Certificate Requirements

| Certificate | Purpose | Owner |
|----------|----------|----------|
| vCenter Server Certificate | Secure management access | PKI/Security Team |
| NSX-T Manager Certificate | Secure API/UI access | PKI/Security Team |
| Aria Suite Appliance Certificates | Secure automation/orchestration endpoints | PKI/Security Team |
| Vault Server Certificate (TLS) | Secure secrets management transport | Security/Vault Team |
| Service Broker API Gateway Certificate | Secure external API consumption | Platform Engineering |

## 5.8 Firewall & Network Dependencies

- Firewall Rules: Management-to-management communication between vCenter, NSX-T, SDDC Manager, Aria Suite, Vault, Backup and DR platforms
- Proxy Requirements: Outbound proxy for VMware update repositories and public cloud connectivity (VMC/HCX), if used
- Load Balancer Dependencies: NSX-T load balancer or external LB fronting Aria Automation/Service Broker API endpoints
- Required Ports: Standard VMware, NSX-T, Vault, backup and DR product ports per vendor documentation (see Section 16.1)
- External Endpoints: VMware update/patch repositories, Trend Micro update servers, Nessus feed servers, public cloud (VMC) endpoints

## 5.9 External Dependencies

- Active Directory: Identity source for RBAC across vCenter, NSX-T, Aria Suite, Vault
- LDAP: Alternative/secondary identity integration where AD is not used
- DNS: Enterprise DNS services
- Monitoring Platform: Aria Operations, Aria Logs
- Backup Platform: Canopy Enterprise Backup, Avamar, Data Domain
- Vault Solution: HashiCorp Vault (Enterprise)
- External APIs: Service Broker consumer integrations
- Database Platforms: Backend databases for Aria Suite components (embedded or external PostgreSQL)
- Message Queues: Internal Aria Automation/Orchestrator messaging (embedded)

## 5.10 Licensing Requirements

- Product Licenses: vSphere, vSAN, NSX-T, Aria Suite, Tanzu Kubernetes Grid, SDDC Manager entitlements
- Subscription Entitlements: HashiCorp Vault Enterprise, Trend Micro, Nessus, Canopy Enterprise Backup, Avamar
- License Keys: Recorded in secure license management repository (not this document)

## 5.11 Skills Required

| Skill | Level |
|----------|----------|
| VMware vSphere/vSAN/NSX-T Administration | Expert |
| VMware Aria Suite Automation & Orchestration | Advanced |
| Kubernetes / Tanzu Administration | Advanced |
| HashiCorp Vault Administration | Advanced |
| Backup & DR Administration (SRM, Avamar, Data Domain) | Advanced |
| Python Scripting / CI-CD Pipeline Engineering | Intermediate |
| Network Engineering (NSX-T, Firewalls, Load Balancing) | Advanced |
| Security & Compliance Operations | Intermediate |

---

# 6. Input Parameters

| Parameter | Value | Description |
|----------|----------|----------|
| environment_name | e.g. `prod-region1` | Target environment identifier used by `provision_infrastructure`, `deploy_configuration_baseline` |
| workflow_name | e.g. `platform-baseline-workflow` | Automation workflow identifier used by `execute_platform_workflow`, `validate_automation_results` |
| region | e.g. `region1` | Target region for network foundation deployment (`deploy_network_foundation`) |
| cluster_name | e.g. `tkg-prod-cluster01` | Kubernetes cluster identifier for `deploy_kubernetes_platform` |
| workload_name | e.g. `app-tier-01` | Workload identifier for backup scheduling (`schedule_backup_job`, `execute_backup`) |
| application_name | e.g. `crm-app` | Application identifier for DR planning (`create_recovery_plan`, `validate_recovery_objectives`) |
| target_site | e.g. `dr-site-b` | Target DR site for `execute_site_failover` |
| namespace_name | e.g. `platform-secrets-ns` | Vault namespace identifier for `create_vault_namespace` |
| key_name | e.g. `platform-cmk-01` | Encryption key identifier for `create_customer_managed_key`, `rotate_encryption_key` |
| service_name | e.g. `vm-provisioning-service` | Platform service name for key assignment / catalog offering |
| catalog_name | e.g. `enterprise-service-catalog` | Catalog identifier for `publish_service_catalog` |
| api_name | e.g. `vm-provisioning-api` | API endpoint identifier for `register_platform_api` |
| subscription_id | e.g. `sub-00123` | Subscription identifier for `validate_api_subscription` |

---

# 7. Build Overview

## 7.1 Deployment Flow

```text
Prepare → Install → Configure → Validate → Handover
```

## 7.2 Build Phases

- Preparation: Infrastructure readiness, network fabric, access, credentials, licensing
- Installation: Core SDDC (vSphere/vSAN/NSX-T), Aria Suite, Tanzu, Vault, Backup, DR, Service Broker deployment
- Configuration: Baseline configuration, security hardening, integration with monitoring/backup/DR/vault
- Integration: Automation workflow execution, Kubernetes/AI/Data platform provisioning, service catalog publication
- Validation: Health checks, connectivity, functional and observability validation

---

# 8. Installation Procedure

## 8.1 Installation Overview

Installation is **hybrid**: initial SDDC foundation components (ESXi, vCenter, NSX-T, SDDC Manager) are deployed using vendor installation media/appliance OVAs, while subsequent platform layers (automation baselines, Kubernetes, AI/data platforms, backup, DR, vault, service broker) are deployed via the repository automation modules (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) invoked through CI/CD or orchestrated automation pipelines.

## 8.2 Step-by-Step Installation

| Step | Action | Estimated Duration | Notes |
|----------|----------|----------|----------|
| 1 | Validate infrastructure, network, DNS, NTP and access pre-requisites | 1 day | Refer to Section 5 |
| 2 | Deploy/validate ESXi hosts, vCenter Server and cluster configuration | 1–2 days | Foundation for compute/storage capability |
| 3 | Configure vSAN datastore and storage policies | 0.5 day | Storage capability |
| 4 | Deploy NSX-T Manager and configure network foundation via `deploy_network_foundation(region)` | 1 day | Networking capability |
| 5 | Deploy SDDC Manager and vLCM baselines | 0.5 day | Lifecycle management capability |
| 6 | Deploy Aria Suite Lifecycle Manager and provision Aria Automation, Orchestrator, Operations, Logs, Network Insight | 1–2 days | Automation/monitoring capability |
| 7 | Execute `provision_infrastructure(environment_name)` to provision environment resources | 0.5 day | Automation module |
| 8 | Execute `deploy_configuration_baseline(environment_name)` to apply standard configuration baselines | 0.5 day | Automation module |
| 9 | Execute `execute_platform_workflow(workflow_name)` and `validate_automation_results(workflow_name)` | 0.5 day | Automation validation |
| 10 | Deploy HashiCorp Vault and execute `create_vault_namespace(namespace_name)`, `create_customer_managed_key(key_name)`, `assign_key_to_service(key_name, service_name)`, `validate_vault_policy(policy_name)` | 1 day | Security capability |
| 11 | Deploy Tanzu Kubernetes Grid via `deploy_kubernetes_platform(cluster_name)` | 1 day | Containers capability |
| 12 | Deploy AI platform services via `deploy_ai_platform(environment)` (if in scope) | 0.5–1 day | Optional, environment-dependent |
| 13 | Deploy data platform services via `deploy_data_platform(environment)` (if in scope) | 0.5–1 day | Optional, environment-dependent |
| 14 | Configure backup platform (Canopy/Avamar/Data Domain) and execute `schedule_backup_job(workload_name)`, `execute_backup(workload_name)`, `validate_backup_integrity(backup_id)` | 1 day | Backup capability |
| 15 | Configure DR platform (SRM/vSphere Replication) and execute `create_recovery_plan(application_name)`, `validate_recovery_objectives(application_name)` | 1 day | Disaster recovery capability |
| 16 | Deploy Service Broker layer via `publish_service_catalog(catalog_name)`, `register_platform_api(api_name)`, `create_service_offering(service_name)` | 0.5 day | API/service broker capability |
| 17 | Execute `validate_platform_observability(environment)` | 0.5 day | Monitoring/observability validation |
| 18 | Perform end-to-end validation, reporting and handover | 1 day | Refer to Sections 9, 15 |

## 8.3 Platform-Specific Steps

**VMware Foundation**
- Deploy ESXi hosts using certified installation media
- Deploy vCenter Server Appliance and join hosts to cluster
- Enable and configure vSAN
- Deploy and configure NSX-T Manager cluster, transport zones, segments and edge nodes

**Aria Suite**
- Deploy Aria Suite Lifecycle Manager as the entry point for all Aria product deployments
- Onboard Aria Automation, Orchestrator, Operations, Logs and Network Insight through Lifecycle Manager

**Tanzu**
- Enable Workload Management on vSphere
- Deploy Tanzu Kubernetes Grid clusters via `deploy_kubernetes_platform`
- Register clusters with Tanzu Mission Control for lifecycle governance

**Public Cloud Integration (Optional)**
- Deploy HCX for workload mobility where hybrid connectivity to VMC is required
- Configure VMC connectivity and validate network extension

---

# 9. Deployment Procedure

## 9.1 Deployment Overview

Deployment follows a phased strategy: foundation infrastructure is deployed first, followed by automation/orchestration enablement, security/vault integration, workload platform services (Kubernetes/AI/Data), then backup, DR and service broker layers. Each phase is validated before progressing to the next, using the automation module return values (boolean success indicators) as gating criteria.

## 9.2 Deployment Steps

- Provisioning: Execute `provision_infrastructure(environment_name)` to provision target environment resources
- Installation: Deploy platform components per Section 8
- Configuration: Apply configuration baselines via `deploy_configuration_baseline(environment_name)` and integration-specific configuration (Section 10)
- Validation: Execute validation functions (`validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`)

## 9.3 Validation Plan

### Health Checks

- Service Status Validation: Confirm vCenter, NSX-T Manager, SDDC Manager, Aria Suite services report healthy status
- Component Health Validation: Confirm ESXi host health, vSAN cluster health, NSX-T transport node status

### Connectivity Tests

- Network Validation: Confirm NSX-T segment reachability, edge uplink connectivity and inter-cluster communication
- External Dependency Validation: Confirm DNS, NTP, Active Directory/LDAP, Vault, backup and DR platform connectivity

### Functional Validation

- Core Function Verification: Execute `validate_automation_results(workflow_name)` and `validate_platform_observability(environment)`
- Integration Testing: Validate Vault key assignment (`assign_key_to_service`), backup integrity (`validate_backup_integrity`), and DR readiness (`validate_recovery_objectives`, `generate_dr_readiness_report`)
- User Acceptance Testing: Validate self-service catalog and API consumption via `validate_api_subscription`

## 9.4 Acceptance Criteria

The deployment is considered successful when:

- Installation completed successfully across compute, storage, networking, automation, security, Kubernetes, backup, DR and service broker layers
- Services operational and passing health checks
- Validation completed successfully (automation, observability, backup, DR, vault, API subscription checks all return positive results)
- Dependencies operational (DNS, NTP, AD/LDAP, monitoring, backup, vault)
- Customer acceptance completed and sign-off obtained (Section 15.3)

---

# 10. Configuration Steps

## 10.1 System Configuration

- Operating System: Apply hardened ESXi/appliance configuration baselines
- Network Settings: Configure NSX-T segments, transport zones, edge clusters and routing via `deploy_network_foundation`
- Storage Configuration: Configure vSAN storage policies, fault domains and capacity thresholds

## 10.2 Security Configuration

- RBAC: Configure role-based access across vCenter, NSX-T, Aria Suite and Vault per Section 5.4
- IAM: Integrate Active Directory/LDAP for centralized identity
- Certificates: Install and validate certificates per Section 5.7
- Hardening: Apply CIS/STIG baselines; deploy Trend Micro endpoint protection and schedule Nessus vulnerability scans
- Audit Configuration: Enable audit logging to Aria Logs for all management planes and Vault namespace via `create_vault_namespace` and `validate_vault_policy`

## 10.3 Integration Configuration

- APIs: Register platform APIs via `register_platform_api(api_name)` and validate subscriptions via `validate_api_subscription(subscription_id)`
- External Systems: Configure integration with Active Directory, DNS, NTP and public cloud (VMC/HCX) as applicable
- Monitoring Platforms: Configure Aria Operations and Aria Logs integration; validate via `validate_platform_observability(environment)`
- Backup Platforms: Configure Canopy Enterprise Backup, Avamar and Data Domain integration; schedule jobs via `schedule_backup_job(workload_name)`

---

# 11. Post-Installation Tasks

- Monitoring Configuration: Confirm Aria Operations dashboards and Aria Logs pipelines are active
- Backup Configuration: Confirm backup schedules are active and `generate_backup_report()` produces expected output
- Documentation Updates: Update HLD/LLD/OPG references and record deployed versions (Section 4.3)
- CMDB Updates: Register all deployed components, versions and ownership in CMDB
- Operations Handover: Complete Section 15 handover activities

---

# 12. Troubleshooting

| Issue | Cause | Resolution |
|----------|----------|----------|
| `provision_infrastructure` returns failure | Insufficient compute/storage capacity or invalid environment_name | Validate infrastructure pre-requisites (Section 5.1) and re-run with corrected parameters |
| `execute_platform_workflow` fails to complete | Aria Orchestrator workflow dependency not met or service account permissions insufficient | Verify service account permissions (Section 5.4) and workflow dependencies; re-execute |
| `deploy_network_foundation` fails for a region | NSX-T transport zone/edge misconfiguration | Verify NSX-T Manager cluster health and transport node configuration; retry deployment |
| `deploy_kubernetes_platform` fails | Workload Management not enabled or insufficient cluster resources | Verify vSphere Workload Management prerequisites and cluster capacity |
| `validate_backup_integrity` returns failure | Backup job did not complete or Data Domain connectivity issue | Check backup job logs, verify Data Domain/Avamar connectivity, re-run `execute_backup` |
| `execute_site_failover` fails | Recovery plan not validated or replication not current | Run `validate_recovery_objectives` prior to failover; confirm replication status in vSphere Replication/SRM |
| `create_customer_managed_key` fails | Vault namespace not created or insufficient Vault permissions | Confirm `create_vault_namespace` executed successfully and Vault administrator permissions are correct |
| `validate_api_subscription` returns failure | Subscription not registered or Service Broker catalog not published | Confirm `publish_service_catalog` and `register_platform_api` completed successfully before validating subscriptions |
| `validate_platform_observability` fails | Aria Operations/Logs integration incomplete | Verify Aria Operations and Aria Logs connectivity and agent/collector configuration |

---

# 13. Rollback Procedure

## 13.1 Conditions

- Failure Scenarios: Failed automation workflow execution, failed configuration baseline application, failed Kubernetes/AI/Data platform deployment, failed security/vault configuration, failed backup or DR configuration
- Rollback Triggers: Validation function (Section 9.3) returns a failure result that cannot be remediated within the defined change window; critical service unavailability post-deployment; security policy validation failure (`validate_vault_policy`)

## 13.2 Steps

- Backup Restoration: Restore affected component configuration from pre-change backup/snapshot (e.g., vCenter/NSX-T Manager configuration export, Vault namespace backup)
- Configuration Reversal: Revert configuration baseline applied by `deploy_configuration_baseline`; remove partially created resources (Kubernetes clusters, vault namespaces, keys, catalog entries) created during the failed change
- Validation Activities: Re-execute relevant validation functions (`validate_automation_results`, `validate_platform_observability`, `validate_vault_policy`) to confirm the environment has returned to its last known good state; document rollback outcome and root cause before re-attempting deployment

---

# 14. Known Issues

```text
No known issues at the time of publication.
```

---

# 15. Handover and Acceptance

## 15.1 Handover Artifacts

- Configuration Backup: vCenter, NSX-T, SDDC Manager, Aria Suite and Vault configuration exports
- Deployment Logs: Automation and deployment module execution logs (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`)
- Validation Results: Output of Section 9.3 validation activities and `generate_dr_readiness_report()` / `generate_backup_report()`
- Runbooks: Operational runbooks for backup, DR failover, key rotation and workflow execution
- Related Documentation: HLD, LLD, OPG and vendor documentation references (Section 2.4)

## 15.2 Ownership Transfer

- Operations Team: Assumes responsibility for day-to-day platform monitoring, patching and incident response
- Support Team: Assumes responsibility for tier 1/2 support and escalation management
- Service Owner: Assumes overall accountability for platform service delivery and lifecycle management

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
| Monitoring Configured | Completed |
| Alerting Configured | Completed |
| Backup Configured | Completed |
| Recovery Tested | Completed |
| Runbooks Delivered | Pending |
| Ownership Assigned | Pending |
| Escalation Process Defined | Pending |

---

# 16. Appendices

## 16.1 Ports & Protocols

| Source | Destination | Port | Protocol | Purpose |
|----------|----------|----------|----------|----------|
| Admin Workstation | vCenter Server | 443 | TCP/HTTPS | vCenter management access |
| ESXi Hosts | vCenter Server | 902, 443 | TCP | Host management communication |
| ESXi Hosts | ESXi Hosts | 8182, 8301, 8302 | TCP/UDP | vSAN cluster communication |
| Admin Workstation | NSX-T Manager | 443 | TCP/HTTPS | NSX-T management access |
| NSX-T Manager | NSX-T Transport Nodes | 1234, 1235 | TCP | NSX-T control/management plane |
| Admin Workstation | SDDC Manager | 443 | TCP/HTTPS | SDDC lifecycle management |
| Admin Workstation | Aria Automation/Orchestrator | 443, 8281 | TCP/HTTPS | Automation and orchestration access |
| Aria Suite | Aria Operations/Logs | 443 | TCP/HTTPS | Monitoring and log ingestion |
| Automation Services | HashiCorp Vault | 8200 | TCP/HTTPS | Secrets and key management API |
| Backup Services | Data Domain/Avamar | 2049, 111, 443 | TCP | Backup data transfer and management |
| DR Services | SRM/vSphere Replication | 443, 8043, 31031 | TCP | Replication and failover orchestration |
| External Consumers | Service Broker API Gateway | 443 | TCP/HTTPS | Self-service API/catalog access |
| Platform Hosts | NTP Server | 123 | UDP | Time synchronization |
| Platform Hosts | DNS Server | 53 | TCP/UDP | Name resolution |

## 16.2 Network Plan

- VLANs: Dedicated VLANs for Management, vMotion, vSAN, NSX-T Overlay/Underlay, Edge Uplink, and Backup networks
- Subnets: Allocated per site/region design, non-overlapping across management and workload domains
- Routing: NSX-T Tier-0/Tier-1 gateways providing north-south and east-west routing; physical uplinks via ECMP where required
- Network Diagrams: Maintained in the associated HLD/LLD documentation (Section 2.4)
- Firewall Zones: Segregated zones for Management, Workload, DMZ/Edge, and Backup/DR networks with NSX-T Distributed Firewall enforcement

## 16.3 Naming Standards

| Object Type | Naming Convention |
|----------|----------|
| Server | `<env>-<role>-<site>-<seq>` (e.g., `prod-esxi-site1-01`) |
| Database | `<env>-<app>-db-<seq>` (e.g., `prod-aria-db-01`) |
| Network | `<env>-<segment-type>-<region>` (e.g., `prod-overlay-region1`) |

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
| SDDC | Software-Defined Data Center |
| NSX-T | VMware NSX-T Data Center (software-defined networking platform) |
| vSAN | VMware vSAN (software-defined storage platform) |
| TKG | Tanzu Kubernetes Grid |
| SRM | Site Recovery Manager |
| CMK | Customer-Managed Key |
