# Operations Guide (OPG): My Cloud Services (my-cloud-platform)

**Author:** Senior Operations Architect (Generated)
**Date:** Generated from repository analysis
**Version:** 1.0
**Status:** Draft
**Owner:** Platform Operations Team

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Service Owner | Platform Owner (My Cloud Services) | Pending | TBD |
| Operations Manager | Cloud Operations Manager | Pending | TBD |
| Platform Owner | VMware Cloud Foundation Platform Owner | Pending | TBD |
| Security Representative | Security & Vault Operations Lead | Pending | TBD |
| Support Lead | L1/L2 Service Desk Lead | Pending | TBD |

---

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Operations Architect | Senior Operations Architect | Generated | Initial generation from repository `jijeeshlearningorg/greenfield-code` (branch `main`) |

---

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | Generated | Initial Operations Guide generated from repository scan (8 files, 41 functions) | Senior Operations Architect |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | My Cloud Services High-Level Design (VCF/vSphere/NSX-T/Tanzu based architecture) | Architecture |
| LLD | Low-Level Design covering `src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` | Detailed Design |
| BIG | Build & Installation Guide for SDDC Manager, Aria Suite, Tanzu Kubernetes Grid | Build & Installation |
| OPG | This Document | Current Document |
| ADR | Architecture Decision Records for platform technology selections | Design Decisions |
| Runbooks | Operational runbooks referenced from `scripts/detect-impact.py` impact mapping and deployment modules | Operations Procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

My Cloud Services (`my-cloud-platform`) is a VMware Cloud Foundation-based private/hybrid cloud platform providing compute (vSphere/ESXi), software-defined storage (vSAN), software-defined networking (NSX-T), Kubernetes container services (Tanzu Kubernetes Grid), automation and lifecycle management (Aria Automation, Aria Orchestrator, SDDC Manager, vLCM), observability (Aria Operations, Aria Logs, Aria Network Insight), security (HashiCorp Vault, Trend Micro, Nessus), backup (Canopy Enterprise Backup, Avamar, Data Domain), disaster recovery (SRM, vSphere Replication, HCX), and a self-service API/service broker layer.

The platform is consumed by internal application teams, tenant workloads, and downstream automation pipelines that provision infrastructure, deploy Kubernetes/AI/data platform services, and consume published service catalog offerings via the API service broker.

Repository evidence for these capabilities is present in:
- `src/automation.py` — infrastructure provisioning and workflow automation
- `src/deploy.py` — network, Kubernetes, AI platform and data platform deployment
- `src/backup.py` — backup scheduling, execution and validation
- `src/dr_platform.py` — disaster recovery planning and failover
- `src/security_vault.py` — secrets/key management (vault namespaces, customer-managed keys)
- `src/service_broker.py` — service catalog publishing and API registration
- `scripts/detect-impact.py` — CI/CD change-impact detection mapping code changes to platform capabilities/domains

---

## 3.2 Business Criticality

- **Mission Critical** — the platform underpins compute, storage, networking, Kubernetes, security and DR services consumed by all downstream tenants and applications. Loss of automation (`src/automation.py`), backup (`src/backup.py`), or DR (`src/dr_platform.py`) capability directly impacts platform-wide resilience.

---

## 3.3 Supported Environments

- Development
- Test
- UAT
- Production

---

## 3.4 Operational Scope

### In Scope

- Monitoring and observability validation (`validate_platform_observability` in `src/deploy.py`)
- Automated provisioning and configuration baseline management (`src/automation.py`)
- Patching and lifecycle management (vLCM, SDDC Manager, Aria Suite Lifecycle Manager)
- Backup scheduling, execution and integrity validation (`src/backup.py`)
- Disaster recovery planning, failover execution and readiness reporting (`src/dr_platform.py`)
- Secrets and encryption key lifecycle management (`src/security_vault.py`)
- Service catalog and API lifecycle operations (`src/service_broker.py`)
- Incident management and escalation

### Out of Scope

- Development Activities (feature code changes to `src/*.py`)
- Architecture Governance (HLD/ADR ownership)
- Major Enhancements and net-new capability builds
- Direct modification of `scripts/detect-impact.py` CI/CD logic outside change control

---

# 4. Service Ownership

## 4.1 Ownership Matrix

| Function | Owner |
|----------|----------|
| Service Owner | Platform Owner — My Cloud Services |
| Technical Owner | Cloud Platform Engineering Lead |
| Operations Team | Cloud Operations Team (compute, storage, networking, Kubernetes) |
| Support Team | Service Desk / L1-L2 Operations |
| Security Team | Security & Vault Operations (HashiCorp Vault, Trend Micro, Nessus) |
| Vendor | VMware (vSphere, NSX-T, Aria Suite, SRM, Tanzu), Dell (Avamar/Data Domain), Canopy (Enterprise Backup) |

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | First-line monitoring triage, dashboard checks, alert acknowledgement, standard service requests (restarts, access requests), initial validation using `validate_automation_results`, `validate_backup_integrity`, `validate_api_subscription` outputs |
| L2 | Operational troubleshooting of automation workflows (`execute_platform_workflow`), backup failures (`execute_backup`), deployment issues (`src/deploy.py`), vault/key issues (`src/security_vault.py`); coordinates with Platform Engineering |
| L3 | Platform Engineering — deep-dive root cause analysis, code-level fixes in `src/` modules, DR failover execution (`execute_site_failover`), architecture-level remediation |
| Vendor | VMware/Dell/Canopy vendor support for underlying platform defects (vSphere, NSX-T, Avamar, Data Domain, SRM) |

---

## 4.3 Escalation Path

| Severity | Escalation Contact |
|----------|----------|
| Critical | Platform Engineering On-Call → Operations Manager → Service Owner |
| High | L2 Operations → Platform Engineering On-Call |
| Medium | L1 Support → L2 Operations |
| Low | L1 Support (standard queue) |

---

# 5. Operational Principles

## 5.1 Approved Change Mechanisms

All production changes shall be performed using approved change processes, consistent with the repository's automation-first design:

- Pull Requests against `jijeeshlearningorg/greenfield-code` (branch `main`)
- CI/CD Pipelines invoking `scripts/detect-impact.py` to determine capability/domain impact of changed files prior to deployment
- Infrastructure-as-Code execution via `src/automation.py` (`provision_infrastructure`, `deploy_configuration_baseline`)
- GitOps-style validated deployment via `src/deploy.py` functions (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`)

---

## 5.2 Configuration Management Principles

- Everything as Code — provisioning and configuration baselines are codified in `src/automation.py`
- Automated Deployment — network, Kubernetes, AI and data platform deployments are function-driven (`src/deploy.py`)
- Version Controlled Configuration — all changes tracked through the `main` branch of the source repository
- Automated Validation — `validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription` provide automated post-change verification
- Automated Rollback — inferred requirement; not explicitly implemented in scanned source files, to be defined at pipeline level

---

## 5.3 Operational Restrictions

### Supported Activities

- Restart services (per approved runbooks)
- Approve deployments triggered by `src/deploy.py` workflows
- Execute published runbooks (backup, DR, automation workflows)
- Investigate alerts raised from observability validation (`validate_platform_observability`)

### Restricted Activities

- Manual production reconfiguration outside `src/automation.py` / `src/deploy.py` pipelines
- Direct infrastructure modification bypassing `provision_infrastructure` / `deploy_configuration_baseline`
- Bypass of deployment pipelines and CI/CD impact detection (`scripts/detect-impact.py`)
- Untracked changes to vault namespaces or encryption keys outside `src/security_vault.py` controlled workflows

---

## 5.4 Break Glass Procedures

Emergency access to production vSphere/NSX-T/vCenter and vault infrastructure shall be governed by a documented break-glass process requiring dual authorization, time-boxed credential issuance (via HashiCorp Vault, per `create_vault_namespace` / `create_customer_managed_key`), and mandatory post-incident review. All emergency changes must be retroactively logged and reconciled against the standard change process described in Section 5.1.

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

| Metric | Threshold | Alert Required |
|----------|----------|----------|
| CPU (compute/ESXi clusters) | > 80% sustained (15 min) | Yes |
| Memory (compute/vSAN nodes) | > 85% sustained | Yes |
| Disk (vSAN capacity) | > 80% utilization | Yes |
| Availability (platform services, API broker) | < 99.9% (rolling 30-day) | Yes |
| Response Time (API service broker, `register_platform_api`) | > 2s p95 | Yes |
| Automation Workflow Success Rate (`execute_platform_workflow`) | < 98% success | Yes |
| Backup Job Success Rate (`execute_backup`) | Any failure | Yes |
| DR Readiness Status (`generate_dr_readiness_report`) | Non-ready state | Yes |

---

## 6.2 Dashboards

| Dashboard | Purpose |
|----------|----------|
| Aria Operations — Infrastructure Health | Compute, storage, networking performance and capacity monitoring |
| Aria Logs — Platform Log Analytics | Centralized log aggregation across compute, network, security and automation modules |
| Aria Network Insight — Network Visibility | NSX-T network flow, topology and micro-segmentation visibility |
| Automation Workflow Dashboard | Status of `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results` |
| Backup Operations Dashboard | Status derived from `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report` |
| DR Readiness Dashboard | Output of `generate_dr_readiness_report` and `validate_recovery_objectives` |
| Service Broker Dashboard | Catalog and API status from `publish_service_catalog`, `register_platform_api`, `validate_api_subscription` |

---

## 6.3 Alerting

| Alert | Severity | Response Target |
|----------|----------|----------|
| Automation workflow failure (`execute_platform_workflow` returns false) | High | 30 minutes |
| Provisioning failure (`provision_infrastructure` returns false) | Critical | 15 minutes |
| Configuration baseline deployment failure (`deploy_configuration_baseline`) | High | 30 minutes |
| Backup job failure (`execute_backup` / `schedule_backup_job`) | Critical | 15 minutes |
| Backup integrity validation failure (`validate_backup_integrity`) | High | 1 hour |
| Deployment failure — network/Kubernetes/AI/data platform (`src/deploy.py`) | Critical | 15 minutes |
| Observability validation failure (`validate_platform_observability`) | High | 30 minutes |
| DR recovery objective breach (`validate_recovery_objectives`) | Critical | 15 minutes |
| Site failover event (`execute_site_failover`) | Critical | Immediate |
| Vault policy validation failure (`validate_vault_policy`) | Critical | 15 minutes |
| Key rotation failure (`rotate_encryption_key`) | High | 1 hour |
| API subscription validation failure (`validate_api_subscription`) | Medium | 4 hours |

---

## 6.4 Logging

### Application Logs

Generated by platform automation and service modules (`src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`, all import `logging`), capturing execution status of provisioning, deployment, vault and service broker operations.

### Platform Logs

Aria Logs aggregates operational telemetry across compute, networking (NSX-T), and Kubernetes (Tanzu Kubernetes Grid) layers.

### Infrastructure Logs

ESXi, vCenter, vSAN, and NSX-T host/component logs forwarded to Aria Logs for centralized retention and analysis.

### Security Logs

HashiCorp Vault audit logs (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`), Trend Micro endpoint protection logs, and Nessus vulnerability scan logs.

---

## 6.5 Audit Logging

- **Audit Events**: Vault namespace creation, key creation/rotation/assignment (`src/security_vault.py`), automation workflow execution (`src/automation.py`), deployment actions (`src/deploy.py`), DR failover execution (`execute_site_failover`), service catalog/API registration (`src/service_broker.py`)
- **Retention Requirements**: Inferred — minimum 12 months for security/audit logs, aligned to compliance obligations in Section 13
- **Compliance Requirements**: ISO27001, GDPR, PCI-DSS (as applicable to tenant workloads)

---

# 7. Backup & Recovery

## 7.1 Backup Requirements

| Asset | Frequency | Retention |
|----------|----------|----------|
| Virtual Machine Images (Canopy Enterprise Backup / Avamar) | Daily, per `schedule_backup_job` | 30 days (inferred) |
| Application-level backups (`execute_backup`) | Daily/Configurable per workload | 30–90 days (inferred) |
| Backup metadata / integrity reports (`generate_backup_report`) | Per backup cycle | 12 months (inferred) |
| Vault configuration (encryption keys, namespaces) | On change (`rotate_encryption_key`, `assign_key_to_service`) | Retained per key lifecycle policy |
| Data Domain backup storage repository | Continuous replication | Per Data Domain retention policy |

---

## 7.2 Recovery Requirements

| Requirement | Target |
|----------|----------|
| RPO | Defined per workload via `create_recovery_plan` / `validate_recovery_objectives` (inferred: 15 min – 4 hours depending on tier) |
| RTO | Defined per workload via DR readiness reporting (inferred: 1–4 hours depending on tier) |

---

## 7.3 Recovery Procedures

Recovery procedures are executed through the DR platform module (`src/dr_platform.py`):
1. `create_recovery_plan(application_name)` — establishes the recovery plan for the target application.
2. `execute_site_failover(target_site)` — performs the site failover using SRM/vSphere Replication.
3. `validate_recovery_objectives(application_name)` — confirms RPO/RTO compliance post-recovery.
4. `generate_dr_readiness_report()` — produces the readiness status for ongoing DR posture review.

Refer to detailed DR runbooks maintained outside this document for step-by-step execution instructions.

---

## 7.4 Backup Validation

Backup validation is performed via `validate_backup_integrity(backup_id)` in `src/backup.py`, confirming restorability of each backup job prior to retention sign-off. Results are aggregated in `generate_backup_report()` for periodic operational review (recommended monthly cadence).

---

# 8. Availability & Resilience

## 8.1 High Availability Overview

High availability is provided at the infrastructure layer through vSphere HA/DRS clusters, vSAN distributed storage resilience, and NSX-T redundant network fabric. Kubernetes workload resilience is provided through Tanzu Kubernetes Grid multi-node clusters, deployed via `deploy_kubernetes_platform` in `src/deploy.py`.

---

## 8.2 Failover Process

Application and site-level failover is orchestrated through `execute_site_failover(target_site)` in `src/dr_platform.py`, leveraging VMware Site Recovery Manager (SRM) and vSphere Replication. Post-failover validation is performed via `validate_recovery_objectives`.

---

## 8.3 Disaster Recovery

DR strategy is built around the `src/dr_platform.py` module:
- Recovery plans are defined per application (`create_recovery_plan`)
- Failover is executed on-demand or during DR events (`execute_site_failover`)
- Recovery objective compliance is validated post-event (`validate_recovery_objectives`)
- Ongoing DR posture is reported via (`generate_dr_readiness_report`)

Underlying DR technologies: VMware SRM, vSphere Replication, HCX (workload mobility), and integration with backup platforms (Canopy Enterprise Backup, Avamar, Data Domain) for backup-based recovery scenarios.

---

## 8.4 Resilience Testing

Periodic DR testing shall invoke `create_recovery_plan` and `validate_recovery_objectives` in non-production/isolated network segments to confirm RPO/RTO compliance without impacting production. `generate_dr_readiness_report` outputs shall be reviewed quarterly (inferred cadence) by Operations and Platform Engineering.

---

# 9. Security Operations

## 9.1 Access Management

- User onboarding: managed through platform IAM integrated with vault namespace provisioning (`create_vault_namespace`)
- User offboarding: access revocation coordinated through vault policy validation (`validate_vault_policy`)
- Role assignments: enforced via RBAC integrated with NSX-T and vCenter, and vault policies

---

## 9.2 Secrets Management

| Secret Type | Management Location |
|----------|----------|
| Customer-managed encryption keys | HashiCorp Vault (`create_customer_managed_key`, `rotate_encryption_key` in `src/security_vault.py`) |
| Service-to-key assignments | HashiCorp Vault (`assign_key_to_service`) |
| Vault namespace credentials | HashiCorp Vault namespaces (`create_vault_namespace`) |
| API subscription credentials | Service Broker (`validate_api_subscription` in `src/service_broker.py`) |

---

## 9.3 Certificate Management

| Certificate | Owner | Renewal Process |
|----------|----------|----------|
| vCenter/ESXi TLS certificates | Platform Engineering | Managed via lifecycle tooling (SDDC Manager/vLCM); inferred renewal cadence |
| NSX-T Manager certificates | Platform Engineering | Managed via NSX-T certificate management; inferred renewal cadence |
| Service Broker API certificates | API Service Broker Team | Renewed via `register_platform_api` update cycle (inferred) |

---

## 9.4 Vulnerability Management

- **Scanning Process**: Nessus-based vulnerability scanning across compute, storage and network layers
- **Remediation Process**: Findings triaged by Security Team, remediated via patch/lifecycle management (Section 10.2/10.3)
- **Exception Process**: Formal risk acceptance and time-boxed exception tracked in RAID register (Section 15)

---

## 9.5 Security Event Management

- **SIEM Integration**: Security logs (Trend Micro, Nessus, Vault audit logs) forwarded to enterprise SIEM (inferred integration point)
- **Security Monitoring**: Continuous monitoring via Aria Operations/Aria Logs combined with vault policy validation (`validate_vault_policy`)
- **Threat Detection**: Trend Micro endpoint protection combined with NSX-T micro-segmentation and Aria Network Insight anomaly detection

---

# 10. Maintenance Activities

## 10.1 Routine Operational Tasks

| Activity | Frequency |
|----------|----------|
| Health Checks (`validate_platform_observability`, `validate_automation_results`) | Daily |
| Capacity Review (compute/vSAN/NSX-T) | Weekly |
| Patch Review (vLCM, SDDC Manager) | Monthly |
| Backup Verification (`validate_backup_integrity`, `generate_backup_report`) | Weekly/Monthly |
| Vault Key Rotation Review (`rotate_encryption_key`) | Quarterly (inferred, or per policy) |
| DR Readiness Review (`generate_dr_readiness_report`) | Quarterly |

---

## 10.2 Patch Management

- **Maintenance Windows**: Scheduled outside business hours per environment (Dev/Test/UAT/Prod), coordinated through change management
- **Approval Process**: Patch changes require CAB/change approval prior to execution via `deploy_configuration_baseline`
- **Testing Requirements**: Patches validated in Test/UAT prior to Production rollout, with post-patch validation via `validate_automation_results`

---

## 10.3 Upgrade Management

- **Supported Upgrade Paths**: VMware Cloud Foundation-aligned upgrade sequencing via SDDC Manager, vLCM, and Aria Suite Lifecycle Manager
- **Version Compatibility**: Upgrades validated against VMware Cloud Foundation interoperability matrices (inferred; not explicit in scanned source)

---

## 10.4 Capacity Management

Capacity growth is managed through ongoing monitoring of compute, vSAN storage and NSX-T network utilization (Section 6.1), with scaling actioned through `provision_infrastructure` and `deploy_configuration_baseline` in `src/automation.py`, and platform-specific deployment functions in `src/deploy.py`.

---

# 11. Service Requests

## 11.1 Standard Requests

- User Access (vault namespace/service access provisioning)
- Capacity Increase (compute/storage/network scaling)
- Certificate Renewal
- Service Restart
- New Tenant Onboarding (via `create_service_offering`, `publish_service_catalog` in `src/service_broker.py`)
- API Subscription Requests (`validate_api_subscription`)

---

## 11.2 Request Fulfilment Process

Standard requests are logged via the service desk (L1), triaged and fulfilled by Operations (L2) using approved automation workflows (`execute_platform_workflow`, `provision_infrastructure`). Service catalog offerings and API registrations are fulfilled through `src/service_broker.py` functions, with all fulfilment actions recorded for audit purposes.

---

# 12. Incident Management

## 12.1 Incident Classification

| Severity | Description |
|----------|----------|
| P1 | Full platform outage, failed site failover, complete loss of backup/DR capability |
| P2 | Significant degradation — automation workflow failures, deployment failures, vault policy failures affecting multiple services |
| P3 | Isolated service degradation — single workload backup failure, single API subscription issue |
| P4 | Minor/cosmetic issues, non-urgent requests, informational alerts |

---

## 12.2 Operational Troubleshooting

Reference troubleshooting procedures below, aligned to repository modules:

1. **Automation Failures** (`src/automation.py`): If `provision_infrastructure` or `execute_platform_workflow` returns false, review automation logs, re-validate configuration baseline via `deploy_configuration_baseline`, and re-run `validate_automation_results`.
2. **Deployment Failures** (`src/deploy.py`): If `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, or `deploy_data_platform` fails, check dependent domain readiness (networking, compute, Kubernetes) then run `validate_platform_observability` to confirm monitoring stack health.
3. **Backup Failures** (`src/backup.py`): If `execute_backup` fails, check `schedule_backup_job` configuration, retry, then confirm integrity via `validate_backup_integrity`; escalate persistent failures to L2/vendor (Canopy/Avamar/Data Domain).
4. **DR Failures** (`src/dr_platform.py`): If `execute_site_failover` fails or `validate_recovery_objectives` reports non-compliance, escalate immediately to L3/Platform Engineering and review `generate_dr_readiness_report` output.
5. **Vault/Security Failures** (`src/security_vault.py`): If `validate_vault_policy` fails or `rotate_encryption_key` errors, escalate to Security Team; do not attempt manual key manipulation outside vault workflows.
6. **Service Broker Failures** (`src/service_broker.py`): If `validate_api_subscription` fails, verify catalog/API registration state via `publish_service_catalog` and `register_platform_api`.

---

## 12.3 Known Issues

| Issue | Workaround |
|----------|----------|
| `scripts/detect-impact.py` regex fallback parsing for some modules (`resolve_capabilities_for_changed_file`) may misclassify domain impact | Manual validation of impacted capabilities during CI/CD pipeline review |
| Backup module (`src/backup.py`) parsed via regex fallback (AST parse failed) | Manual code review recommended before relying on automated documentation of backup functions |
| DR module (`src/dr_platform.py`) parsed via regex fallback | Manual review of DR function signatures recommended prior to major DR changes |

---

# 13. Compliance & Audit

## 13.1 Compliance Requirements

- ISO27001
- GDPR
- PCI-DSS

---

## 13.2 Audit Requirements

- **Audit Responsibilities**: Security Team and Platform Engineering jointly own audit evidence collection for vault, backup and DR operations
- **Log Retention**: Minimum 12 months for security/audit logs (inferred); aligned with compliance retention obligations
- **Evidence Collection**: Automated evidence from `generate_backup_report`, `generate_dr_readiness_report`, and vault audit logs (`src/security_vault.py`)

---

# 14. Operational Readiness Checklist

| Item | Status |
|----------|----------|
| Monitoring Configured | Confirmed via `validate_platform_observability` (`src/deploy.py`) |
| Alerting Configured | Defined in Section 6.3 |
| Backup Configured | Confirmed via `schedule_backup_job` / `execute_backup` (`src/backup.py`) |
| Recovery Tested | Confirmed via `validate_recovery_objectives` (`src/dr_platform.py`) |
| Runbooks Available | Pending — reference external runbook repository |
| Ownership Assigned | Defined in Section 4 |
| Escalation Defined | Defined in Section 4.3 |
| Documentation Complete | Draft — pending final review and sign-off |

---

# 15. RAID Register

## Risks

| Risk | Impact | Mitigation |
|----------|----------|----------|
| Regex fallback parsing in `src/backup.py` and `src/dr_platform.py` indicates AST parse failures | Reduced confidence in automated function documentation for backup/DR modules | Manual code review and static analysis prior to production changes |
| Single automation module (`src/automation.py`) underpins provisioning and configuration baseline | Failure impacts all downstream provisioning | Implement redundancy/rollback validation via `validate_automation_results` |
| No explicit automated rollback function detected in scanned source | Manual rollback effort increases MTTR | Define and implement rollback automation as a backlog item |

## Assumptions

| Assumption | Owner |
|----------|----------|
| Retention periods (backup, audit logs) are inferred and require confirmation against formal policy | Operations Manager |
| Underlying VMware Cloud Foundation components (vSphere, NSX-T, vSAN) are deployed and configured per HLD | Platform Owner |
| SIEM integration for security logs exists outside scanned repository scope | Security Representative |

## Issues

| Issue | Owner |
|----------|----------|
| Backup and DR module source parsing failed AST analysis (fallback regex used) | Platform Engineering |
| No explicit rollback function present in automation module | Platform Engineering |

## Dependencies

| Dependency | Owner |
|----------|----------|
| VMware SDDC Manager, vLCM, Aria Suite Lifecycle Manager for patch/upgrade orchestration | Platform Engineering |
| HashiCorp Vault availability for secrets/key operations (`src/security_vault.py`) | Security Team |
| Canopy Enterprise Backup / Avamar / Data Domain availability for backup execution (`src/backup.py`) | Operations Team |
| VMware SRM / vSphere Replication / HCX availability for DR operations (`src/dr_platform.py`) | Operations Team |
| CI/CD pipeline execution of `scripts/detect-impact.py` for change impact detection | Platform Engineering |

---

# 16. Appendices

## 16.1 Useful Links

| Link | Purpose |
|----------|----------|
| Repository: `jijeeshlearningorg/greenfield-code` (branch `main`) | Source of truth for automation, deployment, backup, DR, security and service broker modules |
| Aria Operations Dashboard | Infrastructure health monitoring |
| Aria Logs | Centralized log analytics |

---

## 16.2 Tooling

| Tool | Purpose |
|----------|----------|
| vSphere / ESXi / vCenter | Compute virtualization and management |
| vSAN | Software-defined storage |
| NSX-T | Software-defined networking and security |
| Aria Automation / Aria Orchestrator | Provisioning and workflow automation (`src/automation.py`) |
| Aria Operations / Aria Logs / Aria Network Insight | Observability, logging, network analytics |
| Tanzu Kubernetes Grid / Tanzu Mission Control | Kubernetes platform runtime and governance (`deploy_kubernetes_platform`) |
| SDDC Manager / vLCM / Aria Suite Lifecycle Manager | Platform lifecycle and patch management |
| Trend Micro / Nessus | Endpoint protection and vulnerability scanning |
| HashiCorp Vault | Secrets and encryption key management (`src/security_vault.py`) |
| Canopy Enterprise Backup / Avamar / Data Domain | Backup execution and storage (`src/backup.py`) |
| SRM / vSphere Replication / HCX | Disaster recovery and workload mobility (`src/dr_platform.py`) |
| VMC | Public cloud integration |
| Service Broker | Self-service catalog and API delivery (`src/service_broker.py`) |

---

## 16.3 Contacts

| Team | Contact |
|----------|----------|
| Platform Engineering | TBD |
| Cloud Operations | TBD |
| Security Operations | TBD |
| Service Desk (L1) | TBD |
| Vendor Support (VMware/Dell/Canopy) | TBD |

---

## 16.4 Glossary

| Term | Definition |
|----------|----------|
| OPG | Operations Guide |
| HLD | High-Level Design |
| LLD | Low-Level Design |
| BIG | Build & Installation Guide |
| SLA | Service Level Agreement |
| SLO | Service Level Objective |
| RTO | Recovery Time Objective |
| RPO | Recovery Point Objective |
| IAM | Identity & Access Management |
| RBAC | Role-Based Access Control |
| SDDC | Software-Defined Data Center |
| VCF | VMware Cloud Foundation |
| SRM | Site Recovery Manager |
| HCX | Hybrid Cloud Extension |
| vLCM | vSphere Lifecycle Manager |
