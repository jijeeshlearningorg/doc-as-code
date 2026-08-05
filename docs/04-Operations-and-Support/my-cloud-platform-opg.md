# Operations Guide (OPG): My Cloud Services (my-cloud-platform)

**Author:** Senior Operations Architect (Generated)
**Date:** TBD - repository evidence not found.
**Version:** 1.0
**Status:** Draft
**Owner:** Platform Operations Team

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Service Owner | TBD - repository evidence not found. | Pending | |
| Operations Manager | TBD - repository evidence not found. | Pending | |
| Platform Owner | TBD - repository evidence not found. | Pending | |
| Security Representative | TBD - repository evidence not found. | Pending | |
| Support Lead | TBD - repository evidence not found. | Pending | |

---

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| TBD - repository evidence not found. | | | |

---

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | TBD - repository evidence not found. | Initial generated Operations Guide from repository scan of `jijeeshlearningorg/greenfield-code` (branch `main`) | Senior Operations Architect (Generated) |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | TBD - repository evidence not found. | Architecture |
| LLD | TBD - repository evidence not found. | Detailed Design |
| BIG | TBD - repository evidence not found. | Build & Installation |
| OPG | This document | Current Document |
| ADR | TBD - repository evidence not found. | Design Decisions |
| Runbooks | Derived from `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` | Operations Procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

My Cloud Services is a VMware-based cloud platform (`my-cloud-platform`) composed of automation, backup, deployment, disaster recovery, security/vault, and service broker modules. Based on repository evidence, the platform:

- Provisions infrastructure and applies configuration baselines (`src/automation.py`: `provision_infrastructure`, `deploy_configuration_baseline`, `execute_platform_workflow`, `validate_automation_results`).
- Deploys network, Kubernetes, AI, and data platform services (`src/deploy.py`: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`).
- Executes and validates backup jobs (`src/backup.py`: `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`).
- Provides disaster recovery planning and failover (`src/dr_platform.py`: `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`).
- Manages secrets/encryption via an enterprise vault (`src/security_vault.py`: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`).
- Publishes and governs service catalog/API consumption (`src/service_broker.py`: `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`).

The product catalog additionally describes capabilities for compute, storage, networking, monitoring, security, disaster-recovery, backup, containers, multi-tenancy, lifecycle-management, public-cloud-integration, reporting, and api-service-broker, implemented using VMware technologies (vSphere, vSAN, NSX-T, Aria Suite, Tanzu, SDDC Manager, SRM, HCX, etc.) as listed in the Product Technologies catalog.

Consumers: Internal platform teams and tenants consuming services through the API/service broker layer (`src/service_broker.py`).

---

## 3.2 Business Criticality

TBD - repository evidence not found. (No explicit criticality classification present in repository; platform supports compute, networking, backup and DR domains, which are typically business/mission critical infrastructure functions — this is an inference, not a confirmed classification.)

---

## 3.3 Supported Environments

- Development
- Test
- UAT
- Production

TBD - repository evidence not found. (No environment-specific configuration files detected in the scanned repository beyond `environment_name`/`environment` function parameters in `src/automation.py` and `src/deploy.py`.)

---

## 3.4 Operational Scope

### In Scope

- Monitoring and observability validation (`validate_platform_observability` in `src/deploy.py`)
- Automated provisioning and configuration baseline management (`src/automation.py`)
- Backup scheduling, execution, and validation (`src/backup.py`)
- Disaster recovery planning, failover execution, and readiness reporting (`src/dr_platform.py`)
- Secrets and encryption key lifecycle management (`src/security_vault.py`)
- Service catalog publication and API subscription validation (`src/service_broker.py`)
- Impact detection for documentation/change management (`scripts/detect-impact.py`)

### Out of Scope

- Development Activities
- Architecture Governance
- Major Enhancements

---

# 4. Service Ownership

## 4.1 Ownership Matrix

| Function | Owner |
|----------|----------|
| Service Owner | TBD - repository evidence not found. |
| Technical Owner | TBD - repository evidence not found. |
| Operations Team | Platform Operations (inferred from automation/deploy/backup/dr modules) |
| Support Team | TBD - repository evidence not found. |
| Security Team | Owns `src/security_vault.py` domain (security, api-service-broker, automation, kubernetes, lifecycle-management, observability) |
| Vendor | VMware (vSphere, NSX-T, Aria Suite, SDDC Manager, SRM, HCX — per Product Technologies catalog) |

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | Initial monitoring, alert triage, execution of published operational functions (e.g., `validate_automation_results`, `validate_backup_integrity`, `validate_platform_observability`) and escalation of failures. |
| L2 | Investigation of failed deployment/backup/DR functions across modules (`src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`); coordination of remediation using existing automation entry points. |
| L3 | Deep technical investigation of module-level failures, code-level defects in `src/*.py` and `scripts/detect-impact.py`, and engineering-level fixes. |
| Vendor | VMware support for underlying technologies (vSphere, vSAN, NSX-T, SRM, Vault-equivalent, backup appliances) per Product Technologies catalog. |

---

## 4.3 Escalation Path

| Severity | Escalation Contact |
|----------|----------|
| Critical | TBD - repository evidence not found. |
| High | TBD - repository evidence not found. |
| Medium | TBD - repository evidence not found. |
| Low | TBD - repository evidence not found. |

---

# 5. Operational Principles

## 5.1 Approved Change Mechanisms

All production changes shall be performed using approved change processes, including:

- Pull Requests against the `jijeeshlearningorg/greenfield-code` repository
- Automated impact detection via `scripts/detect-impact.py` (`build_impacted_capabilities`, `build_doc_request`) to identify affected capabilities/domains on change
- CI/CD Pipelines (inferred; no pipeline definition files were included in the scanned file set)
- Infrastructure-as-Code equivalent modules (`src/automation.py`, `src/deploy.py`)

---

## 5.2 Configuration Management Principles

- Configuration baselines applied via `deploy_configuration_baseline` (`src/automation.py`)
- Automated provisioning via `provision_infrastructure` (`src/automation.py`)
- Automated validation gates via `validate_automation_results`, `validate_platform_observability`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`
- Version-controlled source repository as the system of record

---

## 5.3 Operational Restrictions

### Supported Activities

- Restart/re-execute automation workflows (`execute_platform_workflow`)
- Approve and trigger deployments (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`)
- Execute published backup/DR/security functions
- Investigate alerts arising from validation function failures

### Restricted Activities

- Manual production reconfiguration outside `src/automation.py` / `src/deploy.py` workflows
- Direct infrastructure modification bypassing `provision_infrastructure`/`deploy_configuration_baseline`
- Bypass of validation functions (`validate_*`)
- Untracked changes outside the source repository

---

## 5.4 Break Glass Procedures

TBD - repository evidence not found. (No emergency access or break-glass procedure defined in repository. `execute_site_failover` in `src/dr_platform.py` is the closest evidenced emergency operational function and should be governed by an approved emergency change process.)

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

| Metric | Threshold | Alert Required |
|----------|----------|----------|
| CPU | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Memory | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Disk | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Availability | TBD - repository evidence not found. | Yes (validated via `validate_platform_observability` in `src/deploy.py`) |
| Response Time | TBD - repository evidence not found. | TBD - repository evidence not found. |

Observability is asserted programmatically via `validate_platform_observability(environment)` in `src/deploy.py`, which is described as validating "monitoring, logging and observability configuration." No numeric thresholds are present in the source repository; the product catalog identifies `aria-operations` and `aria-logs` as the associated monitoring/logging technologies.

---

## 6.2 Dashboards

| Dashboard | Purpose |
|----------|----------|
| TBD - repository evidence not found. | No dashboard definitions detected in repository. Product Technologies catalog references `aria-operations` (infrastructure monitoring and operational analytics) and `aria-network-insight` (network visibility) as candidate dashboard sources. |

---

## 6.3 Alerting

| Alert | Severity | Response Target |
|----------|----------|----------|
| Automation workflow validation failure (`validate_automation_results` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Platform observability validation failure (`validate_platform_observability` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Backup integrity validation failure (`validate_backup_integrity` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Recovery objective validation failure (`validate_recovery_objectives` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |
| Vault policy validation failure (`validate_vault_policy` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |
| API subscription validation failure (`validate_api_subscription` returns `false`) | TBD - repository evidence not found. | TBD - repository evidence not found. |

No alert severity levels or response-time targets exist in the scanned repository; all six `validate_*` functions across `src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py` represent the alert-generating control points found in the codebase.

---

## 6.4 Logging

### Application Logs
Modules `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, and `src/service_broker.py` each declare a `logging` import (per Module Relationships), indicating structured application-level logging is emitted from these modules.

### Platform Logs
Platform-level log aggregation is associated with the `aria-logs` technology per the Product Technologies catalog; no direct repository configuration was found.

### Infrastructure Logs
TBD - repository evidence not found.

### Security Logs
`src/security_vault.py` supports the `security` and `observability` domains and imports `logging`; security-relevant events (vault namespace creation, key rotation, policy validation) are candidate sources for security logs.

---

## 6.5 Audit Logging

- **Audit Events:** Inferred from vault operations (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`) and service broker operations (`register_platform_api`, `validate_api_subscription`).
- **Retention Requirements:** TBD - repository evidence not found.
- **Compliance Requirements:** TBD - repository evidence not found.

---

# 7. Backup & Recovery

## 7.1 Backup Requirements

| Asset | Frequency | Retention |
|----------|----------|----------|
| Workload backups (`schedule_backup_job`, `execute_backup` in `src/backup.py`) | TBD - repository evidence not found. | TBD - repository evidence not found. |

Backup capability is implemented through `src/backup.py`, mapped to domains: backup, lifecycle-management, observability, security, storage. Product Technologies catalog identifies `canopy-enterprise-backup`, `avamar`, and `data-domain` as the associated backup platform/storage technologies.

---

## 7.2 Recovery Requirements

| Requirement | Target |
|----------|----------|
| RPO | TBD - repository evidence not found. |
| RTO | TBD - repository evidence not found. |

Recovery objectives are validated programmatically via `validate_recovery_objectives(application_name)` in `src/dr_platform.py`; no numeric RPO/RTO values exist in the repository.

---

## 7.3 Recovery Procedures

Backup recovery workflow derived from `deployment_flow` evidence in `src/backup.py`:

1. `schedule_backup_job(workload_name)` — schedule the backup job for a workload.
2. `execute_backup(workload_name)` — execute the scheduled backup.
3. `validate_backup_integrity(backup_id)` — validate the resulting backup artifact.
4. `generate_backup_report()` — generate a backup status report.

DR recovery workflow derived from `src/dr_platform.py`:

1. `create_recovery_plan(application_name)` — create the recovery plan for an application.
2. `execute_site_failover(target_site)` — execute failover to the target site.
3. `validate_recovery_objectives(application_name)` — validate recovery objectives were met.
4. `generate_dr_readiness_report()` — generate DR readiness reporting.

Reference: Site Recovery Manager (`srm`) and vSphere Replication (`vsphere-replication`) are the associated DR technologies per the Product Technologies catalog.

---

## 7.4 Backup Validation

Backup validation is performed through `validate_backup_integrity(backup_id)` in `src/backup.py`, and results are consolidated via `generate_backup_report()`. No test cadence or automated validation schedule is defined in the repository.

---

# 8. Availability & Resilience

## 8.1 High Availability Overview

TBD - repository evidence not found. (No HA configuration files or clustering logic found in repository. Underlying platform technologies — vSphere/vSAN/NSX-T — are HA-capable per Product Technologies catalog, but no repository-level HA evidence exists.)

---

## 8.2 Failover Process

Failover is executed through `execute_site_failover(target_site)` in `src/dr_platform.py`, preceded by `create_recovery_plan(application_name)` and followed by `validate_recovery_objectives(application_name)` and `generate_dr_readiness_report()`.

---

## 8.3 Disaster Recovery

DR strategy is implemented in `src/dr_platform.py`, mapped to domains: ai-platform, backup, disaster-recovery, lifecycle-management, observability, security. The module depends operationally on the backup domain (shared domain mapping with `src/backup.py`), meaning backup integrity (`validate_backup_integrity`) is a functional prerequisite for reliable recovery plan execution.

Associated technologies (Product Technologies catalog): `srm` (VMware Site Recovery Manager), `vsphere-replication`, `hcx` (workload mobility).

---

## 8.4 Resilience Testing

TBD - repository evidence not found. (No test schedule for `execute_site_failover` or `validate_recovery_objectives` found in repository. `generate_dr_readiness_report()` is the evidenced mechanism for reporting DR readiness state.)

---

# 9. Security Operations

## 9.1 Access Management

- User onboarding: TBD - repository evidence not found.
- User offboarding: TBD - repository evidence not found.
- Role assignments: Managed indirectly through `assign_key_to_service(key_name, service_name)` and `validate_vault_policy(policy_name)` in `src/security_vault.py`.

---

## 9.2 Secrets Management

| Secret Type | Management Location |
|----------|----------|
| Customer-managed encryption keys | `src/security_vault.py` (`create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`) |
| Vault namespaces | `src/security_vault.py` (`create_vault_namespace`) |
| Vault policies | `src/security_vault.py` (`validate_vault_policy`) |

Underlying secrets platform per Product Technologies catalog: `hashicorp-vault`.

---

## 9.3 Certificate Management

| Certificate | Owner | Renewal Process |
|----------|----------|----------|
| TBD - repository evidence not found. | TBD - repository evidence not found. | TBD - repository evidence not found. |

---

## 9.4 Vulnerability Management

- Scanning Process: TBD - repository evidence not found. (Product Technologies catalog references `nessus` as the vulnerability scanning solution and `trend-micro` for endpoint protection, but no repository integration evidence found.)
- Remediation Process: TBD - repository evidence not found.
- Exception Process: TBD - repository evidence not found.

---

## 9.5 Security Event Management

- SIEM Integration: TBD - repository evidence not found.
- Security Monitoring: Supported by `src/security_vault.py` (domain: security, observability) with `logging` import.
- Threat Detection: TBD - repository evidence not found.

---

# 10. Maintenance Activities

## 10.1 Routine Operational Tasks

| Activity | Frequency |
|----------|----------|
| Health Checks | TBD - repository evidence not found. (Performed via `validate_automation_results`, `validate_platform_observability`) |
| Capacity Review | TBD - repository evidence not found. |
| Patch Review | TBD - repository evidence not found. (`vlcm`, `sddc-manager` referenced in Product Technologies catalog for lifecycle patching) |
| Backup Verification | TBD - repository evidence not found. (Performed via `validate_backup_integrity`, `generate_backup_report`) |

---

## 10.2 Patch Management

- Maintenance Windows: TBD - repository evidence not found.
- Approval Process: Governed by approved change mechanisms (Section 5.1).
- Testing Requirements: Validated through `validate_automation_results` in `src/automation.py`.

Lifecycle management technologies per Product Technologies catalog: `sddc-manager`, `vlcm`, `aria-suite-lifecycle-manager`.

---

## 10.3 Upgrade Management

- Supported Upgrade Paths: TBD - repository evidence not found.
- Version Compatibility: TBD - repository evidence not found.

---

## 10.4 Capacity Management

TBD - repository evidence not found. (No capacity thresholds, scaling logic, or growth-management code found in repository.)

---

# 11. Service Requests

## 11.1 Standard Requests

- User Access
- Capacity Increase
- Certificate Renewal
- Service Restart
- New Tenant Onboarding
- Service Catalog Offering Creation (`create_service_offering` in `src/service_broker.py`)
- API Registration (`register_platform_api` in `src/service_broker.py`)

---

## 11.2 Request Fulfilment Process

Service catalog and API-based requests are fulfilled through `src/service_broker.py`:

1. `publish_service_catalog(catalog_name)` — publish a service catalog.
2. `register_platform_api(api_name)` — register a new platform API endpoint.
3. `create_service_offering(service_name)` — create a self-service catalog offering.
4. `validate_api_subscription(subscription_id)` — validate API consumer subscriptions.

Underlying technology per Product Technologies catalog: `service-broker` (self-service delivery portal), `aria-automation` / `aria-orchestrator` for workflow automation.

---

# 12. Incident Management

## 12.1 Incident Classification

| Severity | Description |
|----------|----------|
| P1 | TBD - repository evidence not found. |
| P2 | TBD - repository evidence not found. |
| P3 | TBD - repository evidence not found. |
| P4 | TBD - repository evidence not found. |

---

## 12.2 Operational Troubleshooting

Troubleshooting chains are derived directly from `function_relationships` and `deployment_flow` evidence, grouped by module.

### 12.2.1 Automation / Provisioning Failures (`src/automation.py`)

Execution chain: `provision_infrastructure(environment_name)` → `deploy_configuration_baseline(environment_name)` → `execute_platform_workflow(workflow_name)` → `validate_automation_results(workflow_name)`

Troubleshooting steps:
1. Confirm `provision_infrastructure` returned `true` for the target `environment_name`.
2. If provisioning succeeded, confirm `deploy_configuration_baseline` applied the standard configuration baseline.
3. Re-run `execute_platform_workflow(workflow_name)` for the affected workflow.
4. Confirm outcome via `validate_automation_results(workflow_name)`; if `false`, escalate to L2/L3 per Section 4.2.

### 12.2.2 Deployment Failures (`src/deploy.py`)

Execution chain (per deployment_flow): `deploy_network_foundation(region)` → `deploy_kubernetes_platform(cluster_name)` → `deploy_ai_platform(environment)` → `deploy_data_platform(environment)` → `validate_platform_observability(environment)`

Troubleshooting steps:
1. Verify `deploy_network_foundation(region)` succeeded — network foundation is the first dependency for all subsequent deployment stages.
2. Verify `deploy_kubernetes_platform(cluster_name)` — Kubernetes platform depends on network foundation.
3. Verify `deploy_ai_platform(environment)` and `deploy_data_platform(environment)` — both depend on a successful Kubernetes/network layer.
4. Run `validate_platform_observability(environment)` as the final gate; a `false` result indicates monitoring/logging/observability misconfiguration and should trigger investigation of `aria-operations`/`aria-logs` integration.

### 12.2.3 Backup Failures (`src/backup.py`)

Execution chain: `schedule_backup_job(workload_name)` → `execute_backup(workload_name)` → `validate_backup_integrity(backup_id)` → `generate_backup_report()`

Troubleshooting steps:
1. Confirm the job was scheduled via `schedule_backup_job(workload_name)`.
2. Confirm `execute_backup(workload_name)` completed.
3. Run `validate_backup_integrity(backup_id)` against the resulting `backup_id`; failure indicates a corrupted or incomplete backup requiring re-execution of `execute_backup`.
4. Use `generate_backup_report()` to confirm overall backup health across workloads.

### 12.2.4 Disaster Recovery Failures (`src/dr_platform.py`)

Execution chain: `create_recovery_plan(application_name)` → `execute_site_failover(target_site)` → `validate_recovery_objectives(application_name)` → `generate_dr_readiness_report()`

Troubleshooting steps:
1. Confirm a recovery plan exists for the affected `application_name` via `create_recovery_plan`.
2. If failover was triggered, confirm `execute_site_failover(target_site)` completed for the correct target site.
3. Run `validate_recovery_objectives(application_name)`; failure indicates RPO/RTO targets were not met (values TBD - repository evidence not found) and should be escalated immediately.
4. Consult `generate_dr_readiness_report()` for platform-wide DR posture.

Note: `src/dr_platform.py` shares the `backup` domain with `src/backup.py` — DR troubleshooting should include verification of the underlying backup chain (12.2.3) as a dependency.

### 12.2.5 Security / Vault Failures (`src/security_vault.py`)

Execution chain: `create_vault_namespace(namespace_name)` → `create_customer_managed_key(key_name)` → `rotate_encryption_key(key_name)` → `assign_key_to_service(key_name, service_name)` → `validate_vault_policy(policy_name)`

Troubleshooting steps:
1. Confirm the vault namespace exists via `create_vault_namespace(namespace_name)`.
2. Confirm the encryption key was created via `create_customer_managed_key(key_name)`.
3. If a rotation issue is suspected, verify `rotate_encryption_key(key_name)` completed successfully.
4. Confirm the key is correctly associated using `assign_key_to_service(key_name, service_name)`.
5. Run `validate_vault_policy(policy_name)` as the final control check; failure indicates a policy misassignment requiring security team escalation (Section 4.1).

### 12.2.6 Service Broker / API Failures (`src/service_broker.py`)

Execution chain (per deployment_flow): `publish_service_catalog(catalog_name)` → `register_platform_api(api_name)` → `create_service_offering(service_name)` → `validate_api_subscription(subscription_id)`

Troubleshooting steps:
1. Confirm the catalog was published via `publish_service_catalog(catalog_name)`.
2. Confirm the API was registered via `register_platform_api(api_name)`.
3. Confirm the offering exists via `create_service_offering(service_name)`.
4. Run `validate_api_subscription(subscription_id)` to confirm consumer access; failure indicates a subscription configuration issue.

### 12.2.7 Change Impact Investigation (`scripts/detect-impact.py`)

When troubleshooting an operational regression correlated with a recent change:

1. `read_changed_files(path)` — identify files changed in the triggering pull request.
2. `resolve_capabilities_for_devices` (`resolve_capabilities_for_changed_file`) — map changed files to impacted capabilities using `path_mapping`.
3. `build_impacted_capabilities(changed_files, path_mapping)` — build the full impacted-capability set.
4. `build_doc_request(mapping, changed_files)` — produce the documentation/operational impact request.
5. Cross-reference impacted capabilities/domains against Sections 12.2.1–12.2.6 to identify the correct troubleshooting chain.

---

## 12.3 Known Issues

| Issue | Workaround |
|----------|----------|
| TBD - repository evidence not found. | TBD - repository evidence not found. |

---

# 13. Compliance & Audit

## 13.1 Compliance Requirements

TBD - repository evidence not found.

---

## 13.2 Audit Requirements

- Audit Responsibilities: TBD - repository evidence not found.
- Log Retention: TBD - repository evidence not found.
- Evidence Collection: Vault and service broker operations (`src/security_vault.py`, `src/service_broker.py`) are the primary evidenced sources of auditable events.

---

# 14. Operational Readiness Checklist

| Item | Status |
|----------|----------|
| Monitoring Configured | Partial — validated via `validate_platform_observability` (`src/deploy.py`); no thresholds defined |
| Alerting Configured | Not Confirmed — no alert severities/targets found in repository |
| Backup Configured | Confirmed — `schedule_backup_job`, `execute_backup` present in `src/backup.py` |
| Recovery Tested | Not Confirmed — no test cadence found for `execute_site_failover` |
| Runbooks Available | Partial — functional chains documented in Section 12.2 from repository functions |
| Ownership Assigned | Not Confirmed — TBD - repository evidence not found. |
| Escalation Defined | Not Confirmed — TBD - repository evidence not found. |
| Documentation Complete | In Progress (this document) |

---

# 15. RAID Register

## Risks

| Risk | Impact | Mitigation |
|----------|----------|----------|
| No numeric RPO/RTO values defined in repository | Recovery expectations cannot be validated against business need | Define RPO/RTO in conjunction with `validate_recovery_objectives` implementation |
| No alert severity/thresholds defined | Delayed incident response | Define thresholds for CPU/Memory/Disk/Availability/Response Time and wire to `validate_*` functions |
| DR (`src/dr_platform.py`) depends on backup domain (`src/backup.py`) integrity | Failed backups may compromise recovery plan validity | Ensure `validate_backup_integrity` passes before relying on `create_recovery_plan`/`execute_site_failover` |

---

## Assumptions

| Assumption | Owner |
|----------|----------|
| Underlying VMware technologies (vSphere, vSAN, NSX-T, SRM, Vault) referenced in Product Technologies catalog are deployed and operable | TBD - repository evidence not found. |
| `logging` imports in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py` are wired to a centralized log platform (`aria-logs`) | TBD - repository evidence not found. |

---

## Issues

| Issue | Owner |
|----------|----------|
| No CI/CD pipeline definitions found in scanned repository files | TBD - repository evidence not found. |
| No environment-specific configuration files found | TBD - repository evidence not found. |

---

## Dependencies

| Dependency | Owner |
|----------|----------|
| `src/deploy.py` deployment sequence depends on `src/automation.py` provisioning completing successfully | Platform Operations |
| `src/dr_platform.py` recovery validity depends on `src/backup.py` backup integrity | Platform Operations / DR Team |
| `src/service_broker.py` API registration depends on `src/security_vault.py` key/policy validation for secured services | Security Team |
| `scripts/detect-impact.py` change-impact detection depends on accurate `path_mapping` configuration | TBD - repository evidence not found. |

---

# 16. Appendices

## 16.1 Useful Links

| Link | Purpose |
|----------|----------|
| TBD - repository evidence not found. | Source repository: `jijeeshlearningorg/greenfield-code` (branch `main`) |

---

## 16.2 Tooling

| Tool | Purpose |
|----------|----------|
| vsphere / esxi / vcenter | Core virtualization platform |
| vsan | Software-defined storage |
| nsx-t | Software-defined networking and security |
| aria-automation / aria-orchestrator | Provisioning and workflow automation (aligned to `src/automation.py`) |
| aria-operations | Infrastructure monitoring and operational analytics (aligned to `validate_platform_observability`) |
| aria-logs | Centralized log aggregation (aligned to `logging` imports across modules) |
| aria-network-insight | Network visibility and analytics |
| tanzu-kubernetes-grid / tanzu-mission-control | Kubernetes runtime and governance (aligned to `deploy_kubernetes_platform`) |
| sddc-manager / vlcm / aria-suite-lifecycle-manager | Lifecycle management and patching |
| trend-micro / nessus | Endpoint protection and vulnerability scanning |
| hashicorp-vault | Secrets and credential management (aligned to `src/security_vault.py`) |
| canopy-enterprise-backup / avamar / data-domain | Backup platform and storage (aligned to `src/backup.py`) |
| srm / vsphere-replication | Disaster recovery and replication (aligned to `src/dr_platform.py`) |
| hcx | Workload mobility/migration |
| vmc | Public cloud integration |
| service-broker | Self-service delivery portal (aligned to `src/service_broker.py`) |

---

## 16.3 Contacts

| Team | Contact |
|----------|----------|
| Platform Operations | TBD - repository evidence not found. |
| Security Team | TBD - repository evidence not found. |
| Support Desk | TBD - repository evidence not found. |
| Vendor (VMware) | TBD - repository evidence not found. |

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

---

## 16.5 Operational Dependency Map (Derived from Module Relationships)

```
scripts/detect-impact.py
   └─ supports: ai-platform, api-service-broker, automation, compute, data-platform, lifecycle-management

src/automation.py  (imports: logging)
   └─ supports: automation, lifecycle-management, observability, security
   └─ feeds into: src/deploy.py (deployment relies on provisioned/configured infrastructure)

src/backup.py
   └─ supports: backup, lifecycle-management, observability, security, storage
   └─ feeds into: src/dr_platform.py (shared domain: backup)

src/deploy.py  (imports: logging)
   └─ supports: ai-platform, api-service-broker, compute, data-platform, kubernetes,
               lifecycle-management, networking, observability, security
   └─ downstream of: src/automation.py

src/dr_platform.py
   └─ supports: ai-platform, backup, disaster-recovery, lifecycle-management, observability, security
   └─ depends on: src/backup.py (backup domain overlap)

src/security_vault.py  (imports: logging)
   └─ supports: api-service-broker, automation, kubernetes, lifecycle-management, observability, security
   └─ feeds into: src/service_broker.py (shared domain: api-service-broker, security)

src/service_broker.py  (imports: logging)
   └─ supports: api-service-broker, lifecycle-management, observability, security
   └─ downstream of: src/security_vault.py
```

Common cross-cutting domains observed across nearly all modules: `lifecycle-management`, `observability`, `security` — indicating these are platform-wide operational concerns rather than module-isolated ones, and should be monitored holistically rather than per-module.

---

## 16.6 End-to-End Operational Workflow (Derived from deployment_flow)

```
1. provision_infrastructure          (src/automation.py)  [provision]
2. deploy_configuration_baseline     (src/automation.py)  [deploy]
3. validate_automation_results       (src/automation.py)  [validate]
4. schedule_backup_job               (src/backup.py)      [backup]
5. execute_backup                    (src/backup.py)      [backup]
6. validate_backup_integrity         (src/backup.py)      [validate/backup]
7. generate_backup_report            (src/backup.py)      [backup]
8. deploy_network_foundation         (src/deploy.py)       [deploy]
9. deploy_kubernetes_platform        (src/deploy.py)       [deploy]
10. deploy_ai_platform               (src/deploy.py)       [deploy]
11. deploy_data_platform             (src/deploy.py)       [deploy]
12. validate_platform_observability  (src/deploy.py)       [validate]
13. create_recovery_plan             (src/dr_platform.py)  [recovery]
14. validate_recovery_objectives     (src/dr_platform.py)  [validate/recovery]
15. validate_vault_policy            (src/security_vault.py) [validate]
16. publish_service_catalog          (src/service_broker.py) [publish]
17. register_platform_api            (src/service_broker.py) [register]
18. validate_api_subscription        (src/service_broker.py) [validate]
```

This sequence represents the evidenced end-to-end operational lifecycle: infrastructure provisioning → configuration → backup readiness → platform deployment (network/Kubernetes/AI/data) → observability validation → DR planning/validation → security policy validation → service catalog publication and API validation.
