# Operations Guide (OPG): My Cloud Platform (My Cloud Services)

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
| Service Owner | Platform Owner (My Cloud Services) | Pending | TBC |
| Operations Manager | Cloud Operations Manager | Pending | TBC |
| Platform Owner | VCS Platform Engineering Lead | Pending | TBC |
| Security Representative | Security & Vault Operations Lead | Pending | TBC |
| Support Lead | Service Desk / L1-L2 Support Lead | Pending | TBC |

---

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Platform Engineering | Technical Reviewer | TBC | Initial generation from repository `jijeeshlearningorg/greenfield-code` |

---

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | TBC | Initial Operations Guide generated from repository scan (branch `main`) | Senior Operations Architect |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | My Cloud Services Architecture Overview (product/capability catalog) | Architecture |
| LLD | Detailed design for `src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` | Detailed Design |
| BIG | Build & Installation guidance for VCF/vSphere/NSX-T/Tanzu stack | Build & Installation |
| OPG | This document | Current Document |
| ADR | Design decisions for automation, backup, DR and security vault modules (inferred) | Design Decisions |
| Runbooks | `scripts/detect-impact.py` (impact detection), operational runbooks (to be published) | Operations Procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

My Cloud Services (`my-cloud-platform`) is a VMware-based private/hybrid cloud platform delivering compute (vSphere/ESXi/vCenter), software-defined storage (vSAN), software-defined networking (NSX-T), Kubernetes container services (Tanzu Kubernetes Grid/Tanzu Mission Control), automation/orchestration (Aria Automation, Aria Orchestrator, SDDC Manager), observability (Aria Operations, Aria Logs, Aria Network Insight), security (HashiCorp Vault, Trend Micro, Nessus), backup (Canopy Enterprise Backup, Avamar, Data Domain), disaster recovery (SRM, vSphere Replication, HCX) and an API/service broker consumption layer (Service Broker).

The repository evidence confirms the following operational domains implemented in code:
- **Automation & Lifecycle**: `src/automation.py` (`provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`)
- **Platform Deployment**: `src/deploy.py` (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`)
- **Backup**: `src/backup.py` (`schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`)
- **Disaster Recovery**: `src/dr_platform.py` (`create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`)
- **Security & Secrets**: `src/security_vault.py` (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
- **API/Service Broker**: `src/service_broker.py` (`publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`)
- **Change Impact Detection**: `scripts/detect-impact.py` (CI/CD helper resolving impacted capabilities from changed files for documentation/change governance)

Consumers are internal platform tenants consuming compute, container, AI/data platform and API-brokered services through the self-service catalog (`publish_service_catalog`, `create_service_offering`).

---

## 3.2 Business Criticality

- **Mission Critical** — the platform underpins compute, storage, networking, container and AI/data services for all downstream tenant workloads. Loss of automation, backup or DR capability directly impacts platform-wide resilience.

---

## 3.3 Supported Environments

- Development
- Test
- UAT
- Production

(Environment names are parameterised in code, e.g. `provision_infrastructure(environment_name)`, `deploy_configuration_baseline(environment_name)`, `deploy_ai_platform(environment)`, `deploy_data_platform(environment)` — inferred to support multi-environment operation.)

---

## 3.4 Operational Scope

### In Scope

- Monitoring and observability validation (`validate_platform_observability`)
- Automated provisioning, workflow execution and configuration baselines (`src/automation.py`)
- Backup scheduling, execution, integrity validation and reporting (`src/backup.py`)
- Disaster recovery planning, failover execution and readiness reporting (`src/dr_platform.py`)
- Secrets/encryption key lifecycle management (`src/security_vault.py`)
- API/service catalog publication and subscription validation (`src/service_broker.py`)
- Incident management, patching, capacity management, security operations

### Out of Scope

- Development of new platform capabilities
- Architecture governance and design authority (HLD/LLD ownership)
- Major feature enhancements to `src/*.py` modules (handled via engineering change process)

---

# 4. Service Ownership

## 4.1 Ownership Matrix

| Function | Owner |
|----------|----------|
| Service Owner | Platform Owner — My Cloud Services |
| Technical Owner | Platform Engineering Lead (automation, deploy, DR, security modules) |
| Operations Team | Cloud Operations Team (monitoring, backup, DR execution) |
| Support Team | Service Desk / L1-L2-L3 Support |
| Security Team | Security & Vault Operations (owns `src/security_vault.py` operations, Nessus, Trend Micro) |
| Vendor | VMware (vSphere, NSX-T, Aria Suite, Tanzu, SRM, vSAN); Dell (Avamar, Data Domain) |

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | First-line triage, alert acknowledgement, standard service requests (access, restarts), initial troubleshooting using published runbooks |
| L2 | Platform operational support — execution of `execute_backup`, `validate_backup_integrity`, `execute_platform_workflow`, dashboard/alert investigation, coordination of scheduled maintenance |
| L3 | Deep technical support — automation module debugging (`src/automation.py`), DR failover execution (`execute_site_failover`), security vault key operations (`rotate_encryption_key`), root cause analysis |
| Vendor | VMware/Dell vendor support for underlying platform defects (vSphere, NSX-T, vSAN, Aria Suite, Avamar, Data Domain, SRM) |

---

## 4.3 Escalation Path

| Severity | Escalation Contact |
|----------|----------|
| Critical | Platform Owner + Operations Manager (immediate, 24x7 bridge) |
| High | Operations Manager + L3 Platform Engineering |
| Medium | L2 Operations Team Lead |
| Low | L1 Service Desk queue |

---

# 5. Operational Principles

## 5.1 Approved Change Mechanisms

All production changes shall be performed using approved change processes, consistent with the automation modules detected in the repository:

- Pull Requests against the `main` branch of `jijeeshlearningorg/greenfield-code`
- CI/CD pipelines invoking `scripts/detect-impact.py` for automated change impact analysis and documentation traceability
- Infrastructure-as-Code driven provisioning via `provision_infrastructure` and `deploy_configuration_baseline` (`src/automation.py`)
- Workflow-driven changes via `execute_platform_workflow`
- GitOps-aligned deployment through `src/deploy.py` functions (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`)

---

## 5.2 Configuration Management Principles

- Everything as Code — provisioning and configuration baselines are code-driven (`src/automation.py`)
- Automated Deployment — network, Kubernetes, AI and data platform deployments are function-driven and repeatable (`src/deploy.py`)
- Version Controlled Configuration — all source under Git in `jijeeshlearningorg/greenfield-code`
- Automated Validation — `validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription` provide automated post-change verification gates

---

## 5.3 Operational Restrictions

### Supported Activities

- Restart services via approved automation workflows
- Approve deployments triggered through `src/deploy.py` functions
- Execute published runbooks (backup, DR failover, key rotation)
- Investigate alerts raised from Aria Operations/Aria Logs (inferred monitoring stack)

### Restricted Activities

- Manual production reconfiguration bypassing `deploy_configuration_baseline`
- Direct infrastructure modification outside `provision_infrastructure`
- Bypass of deployment pipelines (`src/deploy.py`, `scripts/detect-impact.py` governance)
- Untracked changes to encryption keys outside `src/security_vault.py` controlled functions

---

## 5.4 Break Glass Procedures

Emergency access to vCenter, NSX-T Manager, SDDC Manager and HashiCorp Vault must be governed by a documented break-glass process (inferred requirement — not present in source code). Emergency use of `rotate_encryption_key` or `execute_site_failover` outside standard change windows must be logged, approved by the Security Representative/Platform Owner, and reviewed post-incident.

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

| Metric | Threshold | Alert Required |
|----------|----------|----------|
| CPU (vSphere/ESXi hosts) | > 80% sustained (inferred) | Yes |
| Memory (vSphere/ESXi hosts) | > 85% sustained (inferred) | Yes |
| Disk / vSAN Capacity | > 75% utilised (inferred) | Yes |
| Availability (platform services, API broker) | < 99.9% (inferred SLA) | Yes |
| Response Time (Service Broker API) | > 2s p95 (inferred) | Yes |
| Observability Validation | Failure of `validate_platform_observability` | Yes |
| Backup Job Success | Failure of `execute_backup` / `validate_backup_integrity` | Yes |
| DR Readiness | Failure of `validate_recovery_objectives` | Yes |

---

## 6.2 Dashboards

| Dashboard | Purpose |
|----------|----------|
| Aria Operations — Infrastructure Health | Compute, storage (vSAN), networking (NSX-T) performance and capacity |
| Aria Operations — Kubernetes Platform | Tanzu Kubernetes Grid / Tanzu Mission Control cluster health |
| Aria Logs — Centralized Logging | Aggregated platform, application and security logs |
| Aria Network Insight | NSX-T network visibility and flow analytics |
| Backup Reporting Dashboard | Output of `generate_backup_report` (backup job status/coverage) |
| DR Readiness Dashboard | Output of `generate_dr_readiness_report` (recovery readiness posture) |
| Service Broker Usage Dashboard | Catalog publication and API subscription status |

---

## 6.3 Alerting

| Alert | Severity | Response Target |
|----------|----------|----------|
| `validate_platform_observability` failure | Critical | 15 minutes |
| Backup job failure (`execute_backup` returns false) | High | 30 minutes |
| Backup integrity validation failure (`validate_backup_integrity`) | High | 1 hour |
| DR readiness objective breach (`validate_recovery_objectives`) | Critical | 30 minutes |
| Site failover triggered (`execute_site_failover`) | Critical | Immediate |
| Vault policy validation failure (`validate_vault_policy`) | High | 1 hour |
| Encryption key rotation failure (`rotate_encryption_key`) | High | 1 hour |
| API subscription validation failure (`validate_api_subscription`) | Medium | 4 hours |
| Automation workflow validation failure (`validate_automation_results`) | Medium | 4 hours |
| Infrastructure capacity threshold breach (Aria Operations) | Medium | 4 hours |

---

## 6.4 Logging

### Application Logs

Service Broker (`src/service_broker.py`) and Automation (`src/automation.py`) execution logs — capturing catalog publication, workflow execution, and validation outcomes — aggregated via Aria Logs.

### Platform Logs

Deployment logs from `src/deploy.py` functions (network foundation, Kubernetes, AI platform, data platform deployment) and lifecycle logs from SDDC Manager / vLCM.

### Infrastructure Logs

ESXi, vCenter, NSX-T and vSAN logs collected centrally via Aria Logs / Aria Network Insight.

### Security Logs

Vault namespace and key operations (`src/security_vault.py`), Trend Micro endpoint protection events, Nessus vulnerability scan logs.

---

## 6.5 Audit Logging

- **Audit Events**: Vault key creation/rotation/assignment (`create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`), backup execution and integrity checks, DR plan creation and failover execution, service catalog and API registration events
- **Retention Requirements**: Minimum 12 months for security/compliance-relevant logs (inferred — confirm against compliance mandate)
- **Compliance Requirements**: Aligned to organisational ISO27001/GDPR/PCI-DSS obligations (inferred; see Section 13)

---

# 7. Backup & Recovery

## 7.1 Backup Requirements

| Asset | Frequency | Retention |
|----------|----------|----------|
| Workload / VM-level backups (`schedule_backup_job`, `execute_backup`) | Daily (inferred, per `workload_name` schedule) | 30 days (inferred) |
| Application-consistent backups | Daily/Weekly (inferred) | 90 days (inferred) |
| Vault key material / secrets metadata | On change (via `create_customer_managed_key`/`rotate_encryption_key`) | Per key lifecycle policy (inferred) |
| Platform configuration baselines | On deployment (`deploy_configuration_baseline`) | Version controlled indefinitely |

Backup platform: Canopy Enterprise Backup, Avamar, with Data Domain as backup storage target (per product technology catalog).

---

## 7.2 Recovery Requirements

| Requirement | Target |
|----------|----------|
| RPO | Defined per `create_recovery_plan(application_name)` — inferred 24 hours default, tenant-configurable |
| RTO | Defined per `validate_recovery_objectives(application_name)` — inferred 4 hours default, tenant-configurable |

---

## 7.3 Recovery Procedures

Recovery execution is driven by `execute_backup` (restore path) and validated through `validate_backup_integrity`. Backup status and coverage reporting is produced by `generate_backup_report`. Detailed step-by-step restore runbooks should reference these functions and the underlying Avamar/Data Domain restore workflows (to be published separately as operational runbooks).

---

## 7.4 Backup Validation

Backup integrity is validated programmatically via `validate_backup_integrity(backup_id)`. Operations teams must review `generate_backup_report()` output on a scheduled basis (recommended: daily) to confirm job success, and perform periodic test restores to validate recoverability (inferred cadence: quarterly).

---

# 8. Availability & Resilience

## 8.1 High Availability Overview

HA is delivered through the underlying vSphere/vSAN/NSX-T stack (compute, storage, networking redundancy) with platform lifecycle managed by SDDC Manager/vLCM. Observability of platform health is validated via `validate_platform_observability` as part of the deployment pipeline (`src/deploy.py`).

---

## 8.2 Failover Process

Failover is orchestrated through `execute_site_failover(target_site)` in `src/dr_platform.py`, built on VMware Site Recovery Manager (SRM) and vSphere Replication, with HCX supporting workload mobility. Failover execution should always follow a pre-validated recovery plan created via `create_recovery_plan(application_name)`.

---

## 8.3 Disaster Recovery

DR strategy is implemented in `src/dr_platform.py`:
- `create_recovery_plan(application_name)` — defines per-application recovery plan
- `execute_site_failover(target_site)` — executes failover to target DR site
- `validate_recovery_objectives(application_name)` — confirms RPO/RTO compliance
- `generate_dr_readiness_report()` — produces platform-wide DR readiness status

Underlying technologies: VMware Site Recovery Manager (SRM), vSphere Replication, HCX. DR readiness reporting should be reviewed on a scheduled basis by Operations and Platform Owner.

---

## 8.4 Resilience Testing

Periodic DR failover tests should be executed using `execute_site_failover` against non-production target sites, with results captured via `generate_dr_readiness_report()`. Recommended cadence: semi-annual (inferred — confirm against organisational DR test policy).

---

# 9. Security Operations

## 9.1 Access Management

- User onboarding/offboarding governed by platform IAM integrated with vCenter/NSX-T RBAC and Vault namespace access (`create_vault_namespace`)
- Role assignments managed through Vault policies validated by `validate_vault_policy(policy_name)`

---

## 9.2 Secrets Management

| Secret Type | Management Location |
|----------|----------|
| Customer-managed encryption keys | HashiCorp Vault, created via `create_customer_managed_key(key_name)` |
| Service-assigned keys | Vault, assigned via `assign_key_to_service(key_name, service_name)` |
| Vault namespaces | `create_vault_namespace(namespace_name)` |
| API credentials / subscriptions | Service Broker (`validate_api_subscription`) |

---

## 9.3 Certificate Management

| Certificate | Owner | Renewal Process |
|----------|----------|----------|
| Platform TLS certificates (vCenter, NSX-T, Aria Suite) | Platform Engineering / Security Team | Managed renewal process aligned with Vault-issued certificates (inferred) |
| Service Broker API certificates | API/Service Broker Team | Renewal via Vault-managed PKI (inferred) |

---

## 9.4 Vulnerability Management

- **Scanning Process**: Nessus-based vulnerability scanning across the platform estate
- **Remediation Process**: Findings triaged by Security Team; remediation tracked against patch/lifecycle management (Section 10.2)
- **Exception Process**: Documented risk exceptions approved by Security Representative (inferred process)

---

## 9.5 Security Event Management

- **SIEM Integration**: Aria Logs feeding centralized log analytics; Trend Micro endpoint events integrated for threat visibility (inferred integration point)
- **Security Monitoring**: Vault key operations audited (`create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
- **Threat Detection**: Trend Micro anti-malware, Nessus vulnerability scanning results feed operational security monitoring

---

# 10. Maintenance Activities

## 10.1 Routine Operational Tasks

| Activity | Frequency |
|----------|----------|
| Health Checks (`validate_platform_observability`, `validate_automation_results`) | Daily |
| Capacity Review (compute/storage/networking) | Weekly |
| Patch Review (vLCM / SDDC Manager lifecycle) | Monthly |
| Backup Verification (`generate_backup_report`, `validate_backup_integrity`) | Daily |
| DR Readiness Review (`generate_dr_readiness_report`) | Monthly |
| Vault Policy Review (`validate_vault_policy`) | Quarterly |
| Encryption Key Rotation (`rotate_encryption_key`) | Per key rotation policy (inferred: quarterly/annual) |

---

## 10.2 Patch Management

- **Maintenance Windows**: Scheduled outside business hours, coordinated through change management
- **Approval Process**: Changes executed via `execute_platform_workflow` and `deploy_configuration_baseline`, requiring approved change record prior to execution
- **Testing Requirements**: Validation gates (`validate_automation_results`, `validate_platform_observability`) must pass before promotion to production

---

## 10.3 Upgrade Management

- **Supported Upgrade Paths**: VMware Cloud Foundation lifecycle via SDDC Manager and vLCM; Aria Suite Lifecycle Manager for Aria component upgrades; Tanzu Mission Control for TKG cluster lifecycle
- **Version Compatibility**: Must be validated against VMware Interoperability Matrix prior to `provision_infrastructure`/`deploy_configuration_baseline` execution (inferred requirement)

---

## 10.4 Capacity Management

Capacity is monitored via Aria Operations dashboards (Section 6.2) and reviewed as part of routine operational tasks (Section 10.1). Scaling actions are executed through `provision_infrastructure(environment_name)` and platform deployment functions in `src/deploy.py`.

---

# 11. Service Requests

## 11.1 Standard Requests

- User Access (Vault namespace/RBAC)
- Capacity Increase (via `provision_infrastructure`)
- Certificate Renewal
- Service Restart
- New Tenant Onboarding (via `create_service_offering`, `register_platform_api`)

---

## 11.2 Request Fulfilment Process

Requests are logged via the service desk (L1), validated and fulfilled by L2 Operations using approved automation functions (e.g., `create_service_offering`, `publish_service_catalog`, `register_platform_api`), with validation checks (`validate_api_subscription`) confirming successful fulfilment before closure.

---

# 12. Incident Management

## 12.1 Incident Classification

| Severity | Description |
|----------|----------|
| P1 | Full platform outage, DR failover invoked, or critical security breach |
| P2 | Significant degradation — backup failures, observability validation failures, single-domain outage (e.g., Kubernetes platform down) |
| P3 | Partial/localized issue with workaround available (e.g., single service offering unavailable) |
| P4 | Minor issue, cosmetic defect, or informational alert |

---

## 12.2 Operational Troubleshooting

Recommended troubleshooting sequence, aligned to detected repository functions:

1. **Observability failure**: Re-run `validate_platform_observability(environment)`; check Aria Operations/Aria Logs dashboards for underlying signal.
2. **Deployment failure**: Verify sequence in `src/deploy.py` — confirm `deploy_network_foundation` succeeded before retrying `deploy_kubernetes_platform`, `deploy_ai_platform`, or `deploy_data_platform`.
3. **Automation workflow failure**: Inspect `execute_platform_workflow(workflow_name)` output; confirm via `validate_automation_results(workflow_name)`.
4. **Backup failure**: Check `schedule_backup_job`/`execute_backup` logs; re-run `validate_backup_integrity(backup_id)`; review `generate_backup_report()`.
5. **DR/failover issue**: Confirm recovery plan via `create_recovery_plan(application_name)`; validate objectives with `validate_recovery_objectives(application_name)` prior to invoking `execute_site_failover(target_site)`.
6. **Security/Vault issue**: Validate namespace and policy via `create_vault_namespace`/`validate_vault_policy`; confirm key assignment with `assign_key_to_service`.
7. **API/Service Broker issue**: Validate subscription state via `validate_api_subscription(subscription_id)`; confirm catalog/API registration via `publish_service_catalog`/`register_platform_api`.

---

## 12.3 Known Issues

| Issue | Workaround |
|----------|----------|
| `scripts/detect-impact.py` parsed via fallback regex parser (AST parse failures noted for `read_yaml`, `resolve_capabilities_for_changed_file`, etc.) | Manual review of impact detection output recommended until parser reliability is improved |
| Several modules (`backup.py`, `dr_platform.py`) parsed via fallback regex due to AST parse failure | Manual code review recommended for these modules pending source correction |

---

# 13. Compliance & Audit

## 13.1 Compliance Requirements

- ISO27001 (inferred — platform security controls align with security domain: Vault, Trend Micro, Nessus)
- GDPR (inferred — applicable where tenant/customer data is processed on data platform)
- PCI-DSS (inferred — applicable if payment-related workloads are hosted; confirm with compliance team)

---

## 13.2 Audit Requirements

- **Audit Responsibilities**: Security Team owns audit evidence for Vault operations (Section 9.2); Operations Team owns backup/DR audit evidence (Sections 7 and 8)
- **Log Retention**: Minimum 12 months (inferred; confirm against regulatory mandate)
- **Evidence Collection**: `generate_backup_report()`, `generate_dr_readiness_report()`, and Vault policy validation logs (`validate_vault_policy`) serve as primary audit evidence sources

---

# 14. Operational Readiness Checklist

| Item | Status |
|----------|----------|
| Monitoring Configured | Confirm `validate_platform_observability` integrated into deployment pipeline |
| Alerting Configured | Confirm alert routing for failures in backup/DR/vault validation functions |
| Backup Configured | Confirm `schedule_backup_job` active for all production workloads |
| Recovery Tested | Confirm periodic execution of `execute_site_failover` in test mode |
| Runbooks Available | Pending — detailed runbooks to be published referencing Section 12.2 |
| Ownership Assigned | Confirm Section 4.1 roles staffed |
| Escalation Defined | Confirm Section 4.3 contacts populated |
| Documentation Complete | This OPG to be finalized with named owners and confirmed thresholds |

---

# 15. RAID Register

## Risks

| Risk | Impact | Mitigation |
|----------|----------|----------|
| Fallback regex parsing indicates possible code quality/parse issues in `backup.py`, `dr_platform.py`, `detect-impact.py` | Reduced confidence in automated validation of backup/DR logic | Manual code review; improve AST compatibility |
| Inferred thresholds/RPO/RTO not yet confirmed with business | Misaligned recovery expectations | Formal RPO/RTO workshop with Service Owner |
| No explicit alerting module detected in repository | Alerts may rely entirely on Aria Operations configuration outside code | Confirm alert rules configured in Aria Operations and document them |

---

## Assumptions

| Assumption | Owner |
|----------|----------|
| Aria Operations/Aria Logs/Aria Network Insight are configured as the monitoring/logging stack per product technology catalog | Platform Engineering |
| Canopy Enterprise Backup/Avamar/Data Domain constitute the backup platform | Operations Team |
| SRM/vSphere Replication/HCX constitute the DR platform | Platform Engineering |
| Multi-environment support (Dev/Test/UAT/Prod) inferred from parameterised functions | Platform Owner |

---

## Issues

| Issue | Owner |
|----------|----------|
| Several source files failed AST parsing and were processed via regex fallback | Platform Engineering |
| Named owners/contacts not yet populated in this document | Service Owner |

---

## Dependencies

| Dependency | Owner |
|----------|----------|
| VMware vSphere/vSAN/NSX-T platform availability | VMware Vendor Support |
| HashiCorp Vault availability for secrets/key operations | Security Team |
| Avamar/Data Domain backup infrastructure | Operations Team |
| SRM/vSphere Replication DR infrastructure | Platform Engineering |

---

# 16. Appendices

## 16.1 Useful Links

| Link | Purpose |
|----------|----------|
| `jijeeshlearningorg/greenfield-code` (branch `main`) | Source repository |
| `scripts/detect-impact.py` | Change impact detection tooling |

---

## 16.2 Tooling

| Tool | Purpose |
|----------|----------|
| vSphere / ESXi / vCenter | Compute virtualization platform |
| vSAN | Software-defined storage |
| NSX-T | Software-defined networking/security |
| Aria Automation / Aria Orchestrator | Provisioning and workflow automation |
| Aria Operations | Infrastructure monitoring |
| Aria Logs | Centralized log aggregation |
| Aria Network Insight | Network visibility/analytics |
| Tanzu Kubernetes Grid / Tanzu Mission Control | Kubernetes platform and governance |
| SDDC Manager / vLCM | Lifecycle automation |
| Aria Suite Lifecycle Manager | Aria component lifecycle |
| HashiCorp Vault | Secrets/encryption key management |
| Trend Micro | Endpoint protection |
| Nessus | Vulnerability scanning |
| Canopy Enterprise Backup / Avamar / Data Domain | Backup and backup storage |
| SRM / vSphere Replication / HCX | Disaster recovery and workload mobility |
| VMC | Public cloud integration |
| Service Broker | Self-service catalog delivery |

---

## 16.3 Contacts

| Team | Contact |
|----------|----------|
| Platform Engineering | TBC |
| Operations Team | TBC |
| Security & Vault Operations | TBC |
| Service Desk | TBC |
| Vendor Support (VMware/Dell) | TBC |

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
| TKG | Tanzu Kubernetes Grid |
| SRM | Site Recovery Manager |
| VCS | VMware Cloud Services (My Cloud Services) |
