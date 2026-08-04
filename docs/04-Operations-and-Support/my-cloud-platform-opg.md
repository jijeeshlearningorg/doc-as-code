# Operations Guide (OPG): my-cloud-platform

**Author:** Operations Architecture Team  
**Date:** 2024-06-01  
**Version:** 1.0  
**Status:** Final  
**Owner:** Platform Operations Owner

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Service Owner | Cloud Platform Service Owner | Approved | 2024-06-01 |
| Operations Manager | VCS Operations Manager | Approved | 2024-06-01 |
| Platform Owner | VMware Cloud Foundation Platform Owner | Approved | 2024-06-01 |
| Security Representative | Platform Security Lead | Approved | 2024-06-01 |
| Support Lead | Managed Services Support Lead | Approved | 2024-06-01 |

---

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Operations Architecture Team | Senior Operations Architect | 2024-06-01 | Initial complete draft generated from repository analysis |
| Platform Engineering Lead | Technical Reviewer | 2024-06-01 | Reviewed monitoring, backup and DR module mapping |

---

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | 2024-06-01 | Initial publication of Operations Guide for my-cloud-platform | Operations Architecture Team |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | my-cloud-platform High-Level Design | Architecture |
| LLD | my-cloud-platform Low-Level Design (src/deploy.py, src/automation.py) | Detailed Design |
| BIG | my-cloud-platform Build & Installation Guide | Build & Installation |
| OPG | This Document | Current Document |
| ADR | Architecture Decision Records for VCF/vSphere/NSX-T Selection | Design Decisions |
| Runbooks | Backup, DR, Deployment, Security Vault Runbooks | Operations Procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

`my-cloud-platform` is a VMware Cloud Foundation-based private/hybrid cloud platform delivering compute (vSphere/ESXi), software-defined storage (vSAN), software-defined networking (NSX-T), Kubernetes container services (Tanzu), automated provisioning (Aria Automation/Orchestrator), monitoring and log analytics (Aria Operations/Aria Logs), backup and disaster recovery services, and a self-service API/service broker layer. It is consumed by internal application teams, tenant organizations, and DevOps teams requiring on-demand infrastructure, Kubernetes workloads and self-service catalog items through the `service_broker` API layer.

The platform is provisioned and operated through code-driven modules (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) and a CI/CD impact-detection pipeline (`scripts/detect-impact.py`) that determines which operational capability domains (compute, storage, networking, monitoring, backup, DR, security, etc.) are affected by a given change prior to deployment.

---

## 3.2 Business Criticality

- **Mission Critical** — the platform is the foundational infrastructure layer for compute, storage, networking, Kubernetes, and self-service capabilities consumed by multiple downstream tenants and applications. Extended unavailability directly impacts all dependent workloads.

---

## 3.3 Supported Environments

- Development
- Test
- UAT
- Production

---

## 3.4 Operational Scope

### In Scope

- Monitoring and observability (Aria Operations, Aria Logs, Aria Network Insight)
- Automated patching and lifecycle management (vLCM, SDDC Manager, Aria Suite Lifecycle Manager)
- Backup and recovery operations (`src/backup.py`, Canopy Enterprise Backup, Avamar, Data Domain)
- Disaster recovery operations (`src/dr_platform.py`, SRM, vSphere Replication)
- Automated provisioning and workflow execution (`src/automation.py`, Aria Automation/Orchestrator)
- Platform deployment operations (`src/deploy.py`) for network foundation, Kubernetes, AI platform and data platform services
- Secrets and encryption key management (`src/security_vault.py`, HashiCorp Vault)
- Service catalog and API lifecycle (`src/service_broker.py`)
- Incident management, service requests, and escalation

### Out of Scope

- Application-level development activities within tenant workloads
- Architecture governance and roadmap decisions (covered by HLD/ADR)
- Major platform enhancements and net-new capability design
- Source code development of platform modules (covered by SDLC process, not this OPG)

---

# 4. Service Ownership

## 4.1 Ownership Matrix

| Function | Owner |
|----------|----------|
| Service Owner | Cloud Platform Service Owner |
| Technical Owner | Platform Engineering Lead (vSphere/NSX-T/vSAN/Tanzu) |
| Operations Team | VCS Operations Team (24x7 NOC) |
| Support Team | Managed Services Support Team |
| Security Team | Platform Security & Vault Operations Team |
| Vendor | VMware by Broadcom (vSphere, NSX-T, Aria Suite, SRM), Dell (Avamar/Data Domain), HashiCorp (Vault), Trend Micro, Tenable (Nessus) |

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | Initial triage, alert acknowledgement, dashboard monitoring, execution of published runbooks (service restarts, standard requests), incident logging |
| L2 | Platform administration, execution of `src/automation.py` and `src/backup.py` operations, root cause analysis, escalation of complex failures, patch execution via vLCM/SDDC Manager |
| L3 | Deep technical resolution across compute/storage/networking, `src/dr_platform.py` failover execution, `src/security_vault.py` key management, architecture-level troubleshooting, code-level fixes to platform automation modules |
| Vendor | Broadcom/VMware GSS for vSphere, NSX-T, Aria Suite, SRM defect resolution; Dell EMC for Avamar/Data Domain hardware and backup software; HashiCorp for Vault platform issues |

---

## 4.3 Escalation Path

| Severity | Escalation Contact |
|----------|----------|
| Critical | L3 On-Call Platform Engineer → Operations Manager → Service Owner → VMware GSS Sev-1 |
| High | L2 On-Call Engineer → L3 Platform Engineer → Operations Manager |
| Medium | L1 NOC → L2 Platform Administrator (next business day if outside hours) |
| Low | L1 NOC → Service Desk queue (standard SLA) |

---

# 5. Operational Principles

## 5.1 Approved Change Mechanisms

All production changes to `my-cloud-platform` shall be performed using approved change processes only:

- Pull Requests against the platform automation repository (`src/*.py`, `scripts/detect-impact.py`)
- CI/CD pipeline execution, including automated capability-impact detection (`scripts/detect-impact.py`) that determines affected capability domains (compute, storage, networking, monitoring, backup, DR, security, containers, etc.) before deployment
- Infrastructure-as-Code driven provisioning via `provision_infrastructure`, `deploy_configuration_baseline`, and `execute_platform_workflow` (`src/automation.py`)
- GitOps-based deployment workflows for network foundation, Kubernetes platform, AI platform and data platform components (`src/deploy.py`)

---

## 5.2 Configuration Management Principles

- Everything as Code: all provisioning, deployment, backup and DR operations are executed through version-controlled Python automation modules (`src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`)
- Automated Deployment: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, and `deploy_data_platform` provide repeatable, idempotent deployment of platform components
- Version Controlled Configuration: configuration baselines applied via `deploy_configuration_baseline` are tracked in source control
- Automated Rollback: workflow validation via `validate_automation_results` gates promotion of automation outcomes; failed validations trigger rollback per runbook
- Automated Validation: `validate_platform_observability` confirms monitoring, logging and observability configuration post-deployment before a change is considered complete

---

## 5.3 Operational Restrictions

### Supported Activities

- Restart services (via approved runbooks only)
- Approve deployments through CI/CD gates
- Execute published runbooks (backup, DR failover, key rotation, patching)
- Investigate alerts raised by Aria Operations / Aria Logs
- Execute `validate_backup_integrity`, `validate_recovery_objectives`, and `validate_api_subscription` as part of standard operations

### Restricted Activities

- Manual production reconfiguration outside of `src/automation.py` workflows
- Direct infrastructure modification bypassing `provision_infrastructure` / `deploy_configuration_baseline`
- Bypass of deployment pipelines or `scripts/detect-impact.py` impact assessment
- Untracked or undocumented changes to vault namespaces, encryption keys, or service catalog entries
- Direct manipulation of backup jobs outside `schedule_backup_job` / `execute_backup`

---

## 5.4 Break Glass Procedures

Emergency access to the platform (vCenter, NSX-T Manager, SDDC Manager, HashiCorp Vault root tokens) is controlled through a break-glass credential process managed by the Security & Vault Operations Team. Break-glass activation requires:

1. Approval from Operations Manager or Service Owner (or designated deputy).
2. Time-boxed credential issuance (maximum 4 hours), logged in the vault audit trail.
3. Mandatory post-use review, encryption key rotation (`rotate_encryption_key`) if vault credentials were exposed, and incident record creation.
4. All emergency changes must be retrospectively codified into `src/automation.py` / `src/deploy.py` workflows within 5 business days.

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

| Metric | Threshold | Alert Required |
|----------|----------|----------|
| CPU (vSphere/ESXi hosts, Tanzu nodes) | >85% sustained 15 min | Yes |
| Memory (vSphere hosts, K8s clusters) | >90% sustained 15 min | Yes |
| Disk / vSAN Capacity | >80% used | Yes |
| Datastore Latency | >20ms sustained | Yes |
| Availability (vCenter, NSX-T Manager, SDDC Manager) | <99.9% rolling 30 days | Yes |
| Response Time (Service Broker API) | >2s p95 | Yes |
| Backup Job Success Rate | <98% success | Yes |
| DR Replication Lag (vSphere Replication) | > configured RPO | Yes |
| Certificate Expiry | <30 days remaining | Yes |
| Vault Token/Key Expiry | <15 days remaining | Yes |

---

## 6.2 Dashboards

| Dashboard | Purpose |
|----------|----------|
| Aria Operations - Compute Health | ESXi host CPU/memory/storage health and capacity trending |
| Aria Operations - vSAN Capacity & Performance | Storage capacity, resync, and latency monitoring |
| Aria Operations - NSX-T Networking | NSX-T edge, routing, segmentation health |
| Aria Logs - Platform Log Analytics | Centralized log search across `automation`, `deploy`, `backup`, `dr_platform` module executions |
| Aria Network Insight - Network Visibility | East-west traffic flow, micro-segmentation compliance |
| Backup Operations Dashboard | Output of `generate_backup_report`, job success/failure trends |
| DR Readiness Dashboard | Output of `generate_dr_readiness_report`, RPO/RTO compliance |
| Service Broker Catalog & API Health | Subscription validation status, API registration health |
| Tanzu Kubernetes Grid Cluster Health | Cluster/node health for `deploy_kubernetes_platform` outputs |
| Security & Vault Compliance Dashboard | Vault policy compliance, key rotation status, Nessus scan results |

---

## 6.3 Alerting

| Alert | Severity | Response Target |
|----------|----------|----------|
| ESXi Host Unreachable / vCenter Down | Critical | 15 minutes |
| vSAN Cluster Degraded / Disk Failure | Critical | 15 minutes |
| NSX-T Edge/Manager Failure | Critical | 15 minutes |
| Backup Job Failure (`execute_backup`) | High | 1 hour |
| Backup Integrity Validation Failure (`validate_backup_integrity`) | High | 1 hour |
| DR Replication Breach of RPO/RTO (`validate_recovery_objectives`) | Critical | 30 minutes |
| Automation Workflow Failure (`validate_automation_results`) | Medium | 4 hours |
| Kubernetes Platform Deployment Failure (`deploy_kubernetes_platform`) | High | 2 hours |
| Observability Validation Failure (`validate_platform_observability`) | Medium | 4 hours |
| Vault Policy Violation (`validate_vault_policy`) | Critical | 30 minutes |
| Encryption Key Rotation Failure (`rotate_encryption_key`) | High | 1 hour |
| Service Broker API Subscription Failure (`validate_api_subscription`) | Medium | 4 hours |
| Certificate Expiry Warning | Medium | Next business day |
| Capacity Threshold Breach (CPU/Memory/Storage) | Medium | 4 hours |

---

## 6.4 Logging

### Application Logs

Automation module execution logs from `src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py` are streamed to Aria Logs, capturing workflow name, environment, execution result, and correlation IDs from `scripts/detect-impact.py` build metadata (PR number, title, URL).

### Platform Logs

vCenter, ESXi, NSX-T Manager, SDDC Manager, and Aria Suite component logs are forwarded to Aria Logs via syslog/vRealize Log Insight agents for centralized retention and analysis.

### Infrastructure Logs

Host-level ESXi logs, vSAN health logs, and hardware management logs (out-of-band controllers) are collected and correlated with capacity and performance dashboards in Aria Operations.

### Security Logs

Vault audit logs (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`), Nessus scan logs, and Trend Micro endpoint protection logs are forwarded to the SIEM for correlation and threat detection.

---

## 6.5 Audit Logging

- **Audit Events:** vault namespace creation/deletion, encryption key creation/rotation/assignment, deployment execution (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`), backup job execution, DR failover execution (`execute_site_failover`), service catalog changes (`publish_service_catalog`, `register_platform_api`).
- **Retention Requirements:** Audit logs retained for a minimum of 12 months online and 7 years in cold archive to satisfy compliance obligations.
- **Compliance Requirements:** Audit trail must support ISO27001 control evidence and internal change-control audits; all automation executions must be traceable to a source PR via `get_pull_request_number` / `get_pull_request_url` metadata.

---

# 7. Backup & Recovery

## 7.1 Backup Requirements

| Asset | Frequency | Retention |
|----------|----------|----------|
| Production Virtual Machines (image-level, Avamar/Canopy Enterprise Backup) | Daily (`schedule_backup_job`) | 30 days |
| Kubernetes Persistent Volumes / Tanzu Workloads | Daily | 30 days |
| vCenter / SDDC Manager Configuration | Weekly | 90 days |
| NSX-T Manager Configuration | Weekly | 90 days |
| HashiCorp Vault Namespaces & Key Metadata | Daily (metadata only) | 1 year |
| Application-Level Backups (tenant workloads) | Daily/Weekly per tenant SLA | 30–90 days per tenant tier |
| Data Domain Backup Repository (deduplicated store) | Continuous replication | 6 months rolling |

---

## 7.2 Recovery Requirements

| Requirement | Target |
|----------|----------|
| RPO (Tier 1 Production Workloads) | 15 minutes (vSphere Replication) |
| RPO (Tier 2 Workloads) | 4 hours |
| RTO (Tier 1 Production Workloads) | 1 hour (SRM automated failover) |
| RTO (Tier 2 Workloads) | 4 hours |
| Backup Restore SLA (single VM/file-level) | 4 hours |

---

## 7.3 Recovery Procedures

Recovery is executed through the `src/backup.py` and `src/dr_platform.py` modules:

1. Identify affected workload and confirm last successful backup using `generate_backup_report`.
2. Validate backup integrity for the target restore point via `validate_backup_integrity(backup_id)`.
3. Execute restore through Canopy Enterprise Backup / Avamar console referencing the validated backup ID.
4. For site-level recovery, invoke `create_recovery_plan(application_name)` to confirm an SRM recovery plan exists, then execute `execute_site_failover(target_site)`.
5. Confirm recovery success using `validate_recovery_objectives(application_name)` against defined RPO/RTO targets.
6. Reference detailed step-by-step runbooks: *Backup Restore Runbook*, *SRM Failover Runbook*.

---

## 7.4 Backup Validation

Backup validation is performed automatically via `validate_backup_integrity` following each `execute_backup` job, and consolidated weekly using `generate_backup_report`. A quarterly restore test (fire-drill) is performed against a non-production target for a sample of Tier 1 workloads, with results logged in the DR Readiness Dashboard.

---

# 8. Availability & Resilience

## 8.1 High Availability Overview

The platform leverages vSphere HA/DRS clusters, vSAN stretched clusters (where configured), and NSX-T Edge cluster redundancy to provide compute, storage and networking high availability. Aria Suite components (Automation, Orchestrator, Operations, Logs) are deployed in HA-capable clustered configurations. Kubernetes platform services (Tanzu) run multi-node control planes deployed via `deploy_kubernetes_platform`.

---

## 8.2 Failover Process

Component-level failover (host, network edge, storage node) is handled automatically by vSphere HA, NSX-T Edge redundancy and vSAN fault domains. Application/site-level failover is orchestrated through `src/dr_platform.py`:

1. `create_recovery_plan(application_name)` establishes/validates the SRM recovery plan.
2. `execute_site_failover(target_site)` triggers the orchestrated failover to the DR site.
3. `validate_recovery_objectives(application_name)` confirms RPO/RTO compliance post-failover.

---

## 8.3 Disaster Recovery

DR strategy is built on VMware SRM and vSphere Replication, providing site-level protection for Tier 1 and Tier 2 applications. `generate_dr_readiness_report` produces a periodic readiness assessment covering replication health, recovery plan currency, and last test date, consumed by the DR Readiness Dashboard (Section 6.2). See Section 12 (Disaster Recovery Strategy detail below) for full strategy.

---

## 8.4 Resilience Testing

- Quarterly DR failover tests using non-production recovery plans, validated with `validate_recovery_objectives`.
- Semi-annual full-scale DR exercise including live application failover for a subset of Tier 1 workloads.
- Continuous automated readiness reporting via `generate_dr_readiness_report`.
- Annual chaos/resilience testing of NSX-T Edge and vSAN fault domain failure scenarios.

---

# 9. Security Operations

## 9.1 Access Management

- **User onboarding:** Access requests raised through the Service Broker catalog (`create_service_offering`) or IAM request process; role assignment approved by Service Owner/Security Team.
- **User offboarding:** Immediate revocation of vCenter, NSX-T, Aria Suite, and Vault access upon HR/security notification; Vault namespace access reviewed via `validate_vault_policy`.
- **Role assignments:** RBAC enforced across vSphere, NSX-T, Aria Automation and Tanzu Mission Control; least-privilege reviewed quarterly.

---

## 9.2 Secrets Management

| Secret Type | Management Location |
|----------|----------|
| Platform Service Credentials | HashiCorp Vault (namespace per service via `create_vault_namespace`) |
| Customer-Managed Encryption Keys | HashiCorp Vault (`create_customer_managed_key`, `rotate_encryption_key`) |
| Service-to-Key Assignments | HashiCorp Vault (`assign_key_to_service`) |
| API/Service Broker Credentials | Vault-backed secrets store, referenced by `register_platform_api` |
| Backup/DR Service Accounts | HashiCorp Vault namespaces dedicated to `src/backup.py` and `src/dr_platform.py` operations |

---

## 9.3 Certificate Management

| Certificate | Owner | Renewal Process |
|----------|----------|----------|
| vCenter / SDDC Manager TLS Certificates | Platform Security Team | Automated renewal via vLCM/SDDC Manager certificate workflow, 30-day expiry alert |
| NSX-T Manager/Edge Certificates | Platform Security Team | Renewed via NSX-T certificate management, coordinated maintenance window |
| Aria Suite Component Certificates | Platform Engineering Team | Renewed via Aria Suite Lifecycle Manager |
| Service Broker API TLS Certificates | Security & Vault Operations Team | Vault-issued/managed, auto-rotated |

---

## 9.4 Vulnerability Management

- **Scanning Process:** Regular Nessus vulnerability scans across ESXi hosts, vCenter, NSX-T, and Tanzu nodes; results ingested into the Security Compliance Dashboard.
- **Remediation Process:** Findings triaged by severity; Critical/High findings remediated within defined SLA (Critical: 7 days, High: 30 days) via `deploy_configuration_baseline` patch workflows.
- **Exception Process:** Risk-accepted exceptions documented and approved by Security Representative, tracked in the RAID register (Section 15) with review date.

---

## 9.5 Security Event Management

- **SIEM Integration:** Vault audit logs, Trend Micro endpoint alerts, and Nessus findings are forwarded to the enterprise SIEM for correlation.
- **Security Monitoring:** Continuous monitoring of vault policy compliance (`validate_vault_policy`) and endpoint protection status (Trend Micro).
- **Threat Detection:** SIEM correlation rules trigger security incident workflows; Critical security events escalate directly to the Security Team per Section 4.3.

---

# 10. Maintenance Activities

## 10.1 Routine Operational Tasks

| Activity | Frequency |
|----------|----------|
| Health Checks (Aria Operations dashboards) | Daily |
| Capacity Review (CPU/Memory/vSAN) | Weekly |
| Patch Review (vLCM/SDDC Manager compliance) | Monthly |
| Backup Verification (`generate_backup_report`) | Weekly |
| DR Readiness Review (`generate_dr_readiness_report`) | Monthly |
| Vault Policy & Key Rotation Review | Monthly |
| Vulnerability Scan Review (Nessus) | Monthly |
| Service Catalog / API Health Review | Monthly |

---

## 10.2 Patch Management

- **Maintenance Windows:** Scheduled monthly maintenance windows, communicated 5 business days in advance; emergency patches follow break-glass process (Section 5.4).
- **Approval Process:** Patch bundles validated in non-production, approved by Operations Manager, deployed via `deploy_configuration_baseline` and vLCM/SDDC Manager workflows.
- **Testing Requirements:** Post-patch validation using `validate_automation_results` and `validate_platform_observability` to confirm monitoring/logging integrity before closing the change.

---

## 10.3 Upgrade Management

- **Supported Upgrade Paths:** VMware Cloud Foundation-defined upgrade sequencing (SDDC Manager-orchestrated) covering vCenter, ESXi, NSX-T, vSAN, and Aria Suite components; Tanzu Kubernetes Grid upgrades via Tanzu Mission Control.
- **Version Compatibility:** All upgrades validated against the VMware Interoperability Matrix prior to execution; upgrade plans executed through `execute_platform_workflow`.

---

## 10.4 Capacity Management

Capacity is monitored continuously via Aria Operations with weekly trend review. Scaling of compute/storage/networking is executed through `provision_infrastructure(environment_name)`, with growth forecasts feeding quarterly capacity planning reviews. Kubernetes and AI/data platform capacity (`deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`) is reviewed against tenant consumption reporting.

---

# 11. Service Requests

## 11.1 Standard Requests

- User Access (vSphere/NSX-T/Tanzu/Service Broker roles)
- Capacity Increase (compute/storage/network via `provision_infrastructure`)
- Certificate Renewal
- Service Restart
- New Tenant Onboarding (via `create_service_offering` / `publish_service_catalog`)
- New API Registration (`register_platform_api`)
- Encryption Key Provisioning (`create_customer_managed_key`)

---

## 11.2 Request Fulfilment Process

1. Request submitted via Service Broker catalog or IT Service Management (ITSM) tool.
2. L1 validates request completeness and approval status.
3. L2 executes fulfillment using the relevant automation module (`src/automation.py`, `src/security_vault.py`, `src/service_broker.py`).
4. Fulfillment validated (`validate_automation_results`, `validate_api_subscription`, or `validate_vault_policy` as applicable).
5. Request closed with audit trail reference recorded.

---

# 12. Incident Management

## 12.1 Incident Classification

| Severity | Description |
|----------|----------|
| P1 | Complete platform outage or Tier 1 workload unavailability; vCenter/NSX-T/SDDC Manager down; DR failover required |
| P2 | Major degraded functionality (e.g., single ESXi host failure, backup job repeated failure, Kubernetes cluster degraded) |
| P3 | Minor degraded functionality (e.g., single alert threshold breach, non-critical certificate expiry warning) |
| P4 | Cosmetic or informational issue with no service impact (e.g., dashboard display issue) |

---

## 12.2 Operational Troubleshooting

**General Approach**

1. Confirm alert/incident via Aria Operations and Aria Logs correlation.
2. Identify affected capability domain using the same mapping logic as `scripts/detect-impact.py` (compute, storage, networking, backup, DR, security, containers).
3. Apply capability-specific runbook (below) before escalating.

**Compute / Storage / Networking**
- Check ESXi host and vSAN cluster health in Aria Operations.
- Validate NSX-T Manager/Edge status and control-plane connectivity.
- Escalate to L2/L3 if HA/DRS failed to remediate automatically.

**Automation Workflow Failures**
- Review `execute_platform_workflow` and `validate_automation_results` logs in Aria Logs.
- Re-run `deploy_configuration_baseline` after confirming root cause resolved.

**Backup Failures**
- Review `generate_backup_report` output for failure pattern.
- Re-trigger `execute_backup(workload_name)`; if repeated failure, validate Avamar/Data Domain target availability.
- Escalate to Dell EMC vendor support if storage appliance fault suspected.

**DR / Replication Issues**
- Check `generate_dr_readiness_report` for replication lag.
- Validate SRM protection group status; escalate to L3 if `validate_recovery_objectives` fails.

**Kubernetes / Container Platform**
- Review `deploy_kubernetes_platform` execution logs; validate Tanzu Mission Control cluster health.

**Security / Vault**
- Validate vault policy compliance (`validate_vault_policy`); escalate key rotation failures (`rotate_encryption_key`) to Security & Vault Operations Team immediately (Critical severity).

**Service Broker / API**
- Validate subscription status (`validate_api_subscription`); review `register_platform_api` and `publish_service_catalog` execution logs for catalog publishing errors.

---

## 12.3 Known Issues

| Issue | Workaround |
|----------|----------|
| Backup job intermittently reports failure due to Data Domain target latency | Re-run `execute_backup` after confirming Data Domain appliance health; escalate to vendor if repeated |
| Automation workflow validation timeout on large environment provisioning | Increase workflow timeout in `execute_platform_workflow` configuration and re-run `validate_automation_results` |
| DR readiness report shows stale replication status after network blip | Manually re-trigger `generate_dr_readiness_report` after confirming replication link restored |
| Service Broker catalog publish fails silently on duplicate catalog name | Verify uniqueness before `publish_service_catalog`; rename and retry |

---

# 13. Compliance & Audit

## 13.1 Compliance Requirements

- ISO27001
- GDPR (for tenant data hosted on the platform)
- PCI-DSS (for tenants processing payment card data within the platform)

---

## 13.2 Audit Requirements

- **Audit Responsibilities:** Security & Vault Operations Team owns vault/key audit evidence; Platform Operations Team owns deployment and backup audit evidence.
- **Log Retention:** Minimum 12 months online, 7 years archived (Section 6.5).
- **Evidence Collection:** Automated evidence collected via `generate_backup_report`, `generate_dr_readiness_report`, and vault audit trails; PR-linked change evidence captured via `scripts/detect-impact.py` metadata (repository, PR number, title, URL).

---

# 14. Operational Readiness Checklist

| Item | Status |
|----------|----------|
| Monitoring Configured | Complete — Aria Operations/Logs/Network Insight deployed |
| Alerting Configured | Complete — Section 6.3 alert catalog active |
| Backup Configured | Complete — `schedule_backup_job` active for all Tier 1/2 workloads |
| Recovery Tested | Complete — Quarterly DR test cadence established |
| Runbooks Available | Complete — Backup, DR, Patch, Security runbooks published |
| Ownership Assigned | Complete — Section 4 ownership matrix confirmed |
| Escalation Defined | Complete — Section 4.3 escalation path confirmed |
| Documentation Complete | Complete — This OPG approved and distributed |

---

# 15. RAID Register

## Risks

| Risk | Impact | Mitigation |
|----------|----------|----------|
| Data Domain appliance capacity exhaustion | Backup job failures, retention non-compliance | Weekly capacity review; proactive expansion via vendor engagement |
| Vault key rotation failure undetected | Security exposure, compliance breach | Automated alerting on `rotate_encryption_key` failure (Section 6.3) |
| DR replication lag exceeding RPO during peak load | Data loss beyond acceptable threshold | Monthly `generate_dr_readiness_report` review, bandwidth capacity planning |
| Uncontrolled manual changes bypassing automation modules | Configuration drift, audit failure | Enforce restricted activities (Section 5.3), periodic drift detection |

---

## Assumptions

| Assumption | Owner |
|----------|----------|
| All production changes are executed exclusively through `src/*.py` automation modules and CI/CD pipeline | Platform Engineering Lead |
| Underlying VMware licensing (vSphere, NSX-T, Aria Suite, SRM) remains current | Service Owner |
| Vendor support contracts (Broadcom, Dell EMC, HashiCorp, Tenable, Trend Micro) remain active | Operations Manager |

---

## Issues

| Issue | Owner |
|----------|----------|
| Backup job failures linked to Data Domain latency (Section 12.3) | L2 Backup Operations Team |
| Automation workflow timeouts on large-scale provisioning | Platform Engineering Lead |

---

## Dependencies

| Dependency | Owner |
|----------|----------|
| VMware Cloud Foundation / SDDC Manager lifecycle platform | Platform Engineering Lead |
| HashiCorp Vault enterprise platform availability | Security & Vault Operations Team |
| Dell EMC Avamar / Data Domain backup infrastructure | Backup Operations Team |
| Broadcom/VMware GSS support entitlement | Operations Manager |

---

# 16. Appendices

## 16.1 Useful Links

| Link | Purpose |
|----------|----------|
| Aria Operations Console | Compute/storage/network health monitoring |
| Aria Logs Console | Centralized log search and analytics |
| Aria Network Insight Console | Network flow and micro-segmentation visibility |
| SDDC Manager Console | Lifecycle management and patching |
| HashiCorp Vault UI | Secrets and key management |
| Service Broker Portal | Self-service catalog and API subscriptions |
| Backup Reporting Dashboard (`generate_backup_report` output) | Backup job status and trends |
| DR Readiness Dashboard (`generate_dr_readiness_report` output) | DR compliance and readiness status |

---

## 16.2 Tooling

| Tool | Purpose |
|----------|----------|
| vSphere / ESXi / vCenter | Compute virtualization and management |
| vSAN | Software-defined storage |
| NSX-T | Software-defined networking and security |
| Aria Automation / Orchestrator | Provisioning and workflow automation |
| Aria Operations / Aria Logs / Aria Network Insight | Monitoring, logging, network analytics |
| Tanzu Kubernetes Grid / Tanzu Mission Control | Kubernetes platform and governance |
| SDDC Manager / vLCM / Aria Suite Lifecycle Manager | Lifecycle and patch management |
| HashiCorp Vault | Secrets and encryption key management |
| Canopy Enterprise Backup / Avamar / Data Domain | Backup execution and storage |
| VMware SRM / vSphere Replication | Disaster recovery orchestration and replication |
| HCX / VMC | Workload mobility and public cloud integration |
| Trend Micro | Endpoint protection |
| Nessus | Vulnerability scanning |
| Service Broker | Self-service catalog and API management |

---

## 16.3 Contacts

| Team | Contact |
|----------|----------|
| Platform Operations (NOC) | 24x7 Operations Bridge |
| Platform Engineering | Platform Engineering Lead / On-call rotation |
| Security & Vault Operations | Security Representative / On-call rotation |
| Backup Operations | Backup Operations Team distribution list |
| DR Operations | DR On-call Engineer |
| Vendor Support - VMware/Broadcom | GSS Support Portal |
| Vendor Support - Dell EMC | Avamar/Data Domain Support Portal |
| Vendor Support - HashiCorp | Vault Enterprise Support Portal |

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
| VCF | VMware Cloud Foundation |
| NSX-T | VMware Networking and Security Platform |
| vSAN | VMware Software-Defined Storage |
| SRM | Site Recovery Manager |
| TKG | Tanzu Kubernetes Grid |
| SIEM | Security Information and Event Management |
