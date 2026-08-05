# Operations Guide (OPG): My Cloud Services (my-cloud-platform)

**Author:** Senior Operations Architect
**Date:** TBD - repository evidence not found.
**Version:** 1.0
**Status:** Draft
**Owner:** TBD - repository evidence not found.

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
| 1.0 | TBD | Initial generation from repository `jijeeshlearningorg/greenfield-code` (branch `main`) | Senior Operations Architect |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | TBD - repository evidence not found. | Architecture |
| LLD | TBD - repository evidence not found. | Detailed Design |
| BIG | TBD - repository evidence not found. | Build & Installation |
| OPG | This document | Current Document |
| ADR | TBD - repository evidence not found. | Design Decisions |
| Runbooks | `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` | Operations Procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

My Cloud Services (`my-cloud-platform`) is a VMware-based cloud platform providing compute, storage, networking, automation, monitoring, security, disaster recovery, backup, container, multi-tenancy, lifecycle-management, public-cloud-integration, reporting and API service broker capabilities, as declared in the Product Capabilities catalog.

The repository `jijeeshlearningorg/greenfield-code` implements the operational automation layer for this platform through the following source modules:

- `scripts/detect-impact.py` — Change impact detection across repository domains (ai-platform, api-service-broker, automation, compute, data-platform, lifecycle-management), used to drive documentation and operational impact analysis for pull requests.
- `src/automation.py` — Infrastructure provisioning and platform workflow automation (`provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`).
- `src/backup.py` — Backup scheduling, execution, integrity validation and reporting (`schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`).
- `src/deploy.py` — Platform deployment across network, Kubernetes, AI and data domains, plus observability validation (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`).
- `src/dr_platform.py` — Disaster recovery planning, failover execution, and recovery validation (`create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`).
- `src/security_vault.py` — Secrets/key management operations (`create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`).
- `src/service_broker.py` — Service catalog publication and API subscription management (`publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`).

Consumers of the platform are tenants provisioned via the automation and service broker layers, consistent with the `api-service-broker` and `multi-tenancy` capabilities in the Product Capability catalog.

---

## 3.2 Business Criticality

TBD - repository evidence not found. (No explicit criticality classification present in repository; platform capabilities suggest Business Critical / Mission Critical infrastructure services, but this must be formally confirmed by the Service Owner.)

---

## 3.3 Supported Environments

- Development
- Test
- UAT
- Production

(Environment names are not explicitly declared in the repository; the `environment_name` / `environment` parameters in `provision_infrastructure`, `deploy_configuration_baseline`, `deploy_ai_platform`, `deploy_data_platform`, and `validate_platform_observability` confirm environment-scoped operations. Specific environment identifiers are TBD.)

---

## 3.4 Operational Scope

### In Scope

- Monitoring and observability validation (`validate_platform_observability` in `src/deploy.py`)
- Automation and lifecycle workflows (`src/automation.py`)
- Backup scheduling, execution and validation (`src/backup.py`)
- Disaster recovery planning and failover (`src/dr_platform.py`)
- Secrets/key lifecycle management (`src/security_vault.py`)
- Service catalog and API lifecycle management (`src/service_broker.py`)
- Change impact detection for repository-driven documentation (`scripts/detect-impact.py`)

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
| Operations Team | TBD - repository evidence not found. |
| Support Team | TBD - repository evidence not found. |
| Security Team | Owner of `src/security_vault.py` operations (vault namespace, key rotation, policy validation) — TBD name |
| Vendor | TBD - repository evidence not found. |

Note: Module headers in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, and `src/service_broker.py` reference "Author: Jijeesh Valappil" as the code author; this is a code authorship attribution and not confirmed as an operational ownership role.

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | Monitor observability outputs from `validate_platform_observability`; execute published runbooks for automation, backup, and deployment workflows; raise alerts on function failures (`False` return values) from any operational module. |
| L2 | Investigate failures across `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`; execute `validate_*` functions to diagnose failed workflows; coordinate remediation using the function chains defined in Section 12.2. |
| L3 | Perform root-cause analysis on deployment_flow failures, including `execute_site_failover` and `create_recovery_plan` failures in `src/dr_platform.py`; update automation source and configuration baselines. |
| Vendor | TBD - repository evidence not found. (Underlying platform technologies — vSphere, NSX-T, Aria Suite, SDDC Manager, Tanzu — are catalog-referenced technologies; vendor support model TBD.) |

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

Repository evidence confirms a change-detection driven operational model via `scripts/detect-impact.py`, which reads changed files (`read_changed_files`), resolves impacted capabilities (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`), and generates documentation impact requests (`build_doc_request`) tied to Pull Requests (`get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`).

This confirms the following approved change mechanisms:

- Pull Requests (evidenced by `get_pull_request_*` functions)
- Automated impact detection and documentation triggering (`scripts/detect-impact.py`)
- Infrastructure-as-Code style automation functions (`provision_infrastructure`, `deploy_configuration_baseline`)

---

## 5.2 Configuration Management Principles

- Environment-scoped provisioning via `provision_infrastructure(environment_name)` and `deploy_configuration_baseline(environment_name)` in `src/automation.py`.
- Automated validation gating via `validate_automation_results(workflow_name)` and `validate_platform_observability(environment)`.
- Path-to-capability mapping is centrally configured and read via `read_yaml` in `scripts/detect-impact.py`, indicating configuration-as-code for impact/capability mapping.

---

## 5.3 Operational Restrictions

### Supported Activities

- Execute automation workflows (`execute_platform_workflow`)
- Execute backup jobs (`schedule_backup_job`, `execute_backup`)
- Execute validated deployment functions (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`)
- Execute recovery plans (`create_recovery_plan`, `execute_site_failover`)
- Execute published runbooks derived from the deployment_flow

### Restricted Activities

- Manual production reconfiguration outside `deploy_configuration_baseline`
- Direct modification of vault namespaces/keys outside `src/security_vault.py` functions
- Bypass of `validate_automation_results`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_platform_observability`, or `validate_api_subscription` validation gates
- Untracked changes bypassing `scripts/detect-impact.py` change detection

---

## 5.4 Break Glass Procedures

TBD - repository evidence not found. No break-glass function or emergency access module was identified in the repository. This must be defined by the Operations Team.

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

Repository evidence confirms an `observability` domain applied across `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py` (see Module Relationships). Explicit observability validation exists via `validate_platform_observability(environment)` in `src/deploy.py`, described as "Validates monitoring, logging and observability configuration."

No explicit numeric thresholds (CPU, memory, disk, response time) are present in the repository.

| Metric | Threshold | Alert Required |
|----------|----------|----------|
| CPU | TBD - repository evidence not found. | TBD |
| Memory | TBD - repository evidence not found. | TBD |
| Disk | TBD - repository evidence not found. | TBD |
| Availability | TBD - repository evidence not found. | Yes — inferred from `validate_platform_observability` |
| Response Time | TBD - repository evidence not found. | TBD |
| Automation Workflow Outcome | Boolean success/fail (`validate_automation_results`) | Yes |
| Backup Integrity | Boolean success/fail (`validate_backup_integrity`) | Yes |
| Recovery Objective Compliance | Boolean success/fail (`validate_recovery_objectives`) | Yes |
| Vault Policy Compliance | Boolean success/fail (`validate_vault_policy`) | Yes |
| API Subscription Validity | Boolean success/fail (`validate_api_subscription`) | Yes |

---

## 6.2 Dashboards

| Dashboard | Purpose |
|----------|----------|
| TBD - repository evidence not found. | No dashboard configuration files detected in repository. Product Technology catalog references `aria-operations` (infrastructure monitoring and operational analytics) and `aria-network-insight` (network visibility) as candidate dashboard sources — inferred, not confirmed in repository code. |

---

## 6.3 Alerting

No dedicated alerting module (e.g., alert rules, notification integration) was detected in the repository. Alert requirements below are derived from validation function outcomes present in `deployment_flow`.

| Alert | Severity | Response Target |
|----------|----------|----------|
| `validate_automation_results` returns `False` | TBD | TBD - repository evidence not found. |
| `validate_backup_integrity` returns `False` | TBD | TBD - repository evidence not found. |
| `validate_recovery_objectives` returns `False` | TBD | TBD - repository evidence not found. |
| `validate_platform_observability` returns `False` | TBD | TBD - repository evidence not found. |
| `validate_vault_policy` returns `False` | TBD | TBD - repository evidence not found. |
| `validate_api_subscription` returns `False` | TBD | TBD - repository evidence not found. |
| `execute_site_failover` failure | TBD | TBD - repository evidence not found. |

---

## 6.4 Logging

### Application Logs

`logging` is imported directly in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, and `src/service_broker.py` (see Module Relationships: `imports` → `logging`). This confirms native Python application logging is embedded in automation, deployment, vault, and service broker operations.

### Platform Logs

Product Technology catalog references `aria-logs` (centralized log aggregation and analytics) as the platform-level log aggregation technology — inferred from catalog, not directly referenced in repository source code.

### Infrastructure Logs

TBD - repository evidence not found. No infrastructure-level (ESXi/vSphere/NSX-T) log shipping configuration detected in repository.

### Security Logs

Security-domain logging is implied by `security` domain tagging on `src/security_vault.py`, `src/backup.py`, `src/automation.py`, `src/deploy.py`, and `src/service_broker.py`. No dedicated security log module was found; SIEM integration is TBD.

---

## 6.5 Audit Logging

- Audit Events: TBD - repository evidence not found. (No explicit audit event schema in repository.)
- Retention Requirements: TBD - repository evidence not found.
- Compliance Requirements: TBD - repository evidence not found.

---

# 7. Backup & Recovery

## 7.1 Backup Requirements

Backup operations are implemented in `src/backup.py`, mapped to the `backup` capability (see Capability Mapping) and domains `backup`, `lifecycle-management`, `observability`, `security`, `storage`.

Deployment flow order for backup: `schedule_backup_job` → `execute_backup` → `validate_backup_integrity` → `generate_backup_report`.

| Asset | Frequency | Retention |
|----------|----------|----------|
| Workload backups (`schedule_backup_job(workload_name)`) | TBD - repository evidence not found. | TBD - repository evidence not found. |

Product Technology catalog references `canopy-enterprise-backup`, `avamar`, and `data-domain` as backup platform technologies — inferred from catalog, not directly instantiated in repository code.

---

## 7.2 Recovery Requirements

| Requirement | Target |
|----------|----------|
| RPO | TBD - repository evidence not found. |
| RTO | TBD - repository evidence not found. |

Recovery objective compliance is validated programmatically via `validate_recovery_objectives(application_name)` in `src/dr_platform.py`, but no numeric RPO/RTO values are present in the repository.

---

## 7.3 Recovery Procedures

Recovery procedures follow the function chain in `src/backup.py`:

1. `schedule_backup_job(workload_name)` — schedule backup for the affected workload.
2. `execute_backup(workload_name)` — execute the backup job.
3. `validate_backup_integrity(backup_id)` — verify backup integrity before recovery is attempted.
4. `generate_backup_report()` — produce a report confirming backup state prior to/after recovery.

For site-level recovery, follow the DR chain documented in Section 8.3 and Section 12.2.

---

## 7.4 Backup Validation

Backup validation is performed via `validate_backup_integrity(backup_id)` in `src/backup.py`, returning a boolean success/fail result. `generate_backup_report()` produces a dictionary report used to confirm backup validation status. No automated test-restore schedule was found in the repository — testing cadence is TBD.

---

# 8. Availability & Resilience

## 8.1 High Availability Overview

TBD - repository evidence not found. No HA configuration (clustering, load balancing) was detected directly in repository source. The `compute`, `storage`, and `networking` capabilities in the Product Capability catalog (VMware vSphere/vSAN/NSX-T) imply underlying HA infrastructure, but this is catalog-inferred, not repository-confirmed.

---

## 8.2 Failover Process

Failover is implemented in `src/dr_platform.py` via `execute_site_failover(target_site)`. This function is invoked as part of the deployment_flow following `create_recovery_plan(application_name)`.

Failover Sequence (from deployment_flow / function evidence):

1. `create_recovery_plan(application_name)` — establish the recovery plan for the target application.
2. `execute_site_failover(target_site)` — perform failover to the target site.
3. `validate_recovery_objectives(application_name)` — confirm recovery objective compliance post-failover.
4. `generate_dr_readiness_report()` — produce DR readiness status report.

---

## 8.3 Disaster Recovery

DR capability is implemented in `src/dr_platform.py`, mapped to domains `ai-platform`, `backup`, `disaster-recovery`, `lifecycle-management`, `observability`, `security` (Module Relationships).

### Operational Dependency Diagram (DR Module)

```
src/dr_platform.py
   ├── depends on: ai-platform domain
   ├── depends on: backup domain (src/backup.py)
   ├── supports: disaster-recovery domain
   ├── supports: lifecycle-management domain
   ├── supports: observability domain
   └── supports: security domain
```

### DR Function Chain (from deployment_flow)

```
create_recovery_plan(application_name)
        │
        ▼
execute_site_failover(target_site)
        │
        ▼
validate_recovery_objectives(application_name)
        │
        ▼
generate_dr_readiness_report()
```

Product Technology catalog references `srm` (VMware Site Recovery Manager) and `vsphere-replication` as the underlying DR technologies — inferred from catalog, not directly confirmed in repository code.

---

## 8.4 Resilience Testing

TBD - repository evidence not found. No automated DR test scheduling module was found. `generate_dr_readiness_report()` in `src/dr_platform.py` provides a reportable readiness output that can support periodic resilience test evidence, but no test cadence is defined in the repository.

---

# 9. Security Operations

## 9.1 Access Management

TBD - repository evidence not found for user onboarding/offboarding workflows. Repository evidence confirms service-level key/namespace access controls only, via `src/security_vault.py`.

---

## 9.2 Secrets Management

| Secret Type | Management Location |
|----------|----------|
| Vault namespace (`create_vault_namespace(namespace_name)`) | `src/security_vault.py` |
| Customer-managed encryption keys (`create_customer_managed_key(key_name)`) | `src/security_vault.py` |
| Encryption key rotation (`rotate_encryption_key(key_name)`) | `src/security_vault.py` |
| Key-to-service assignment (`assign_key_to_service(key_name, service_name)`) | `src/security_vault.py` |

Product Technology catalog references `hashicorp-vault` as the enterprise secrets/credential management platform underlying these functions — inferred from catalog.

---

## 9.3 Certificate Management

| Certificate | Owner | Renewal Process |
|----------|----------|----------|
| TBD - repository evidence not found. | | |

---

## 9.4 Vulnerability Management

TBD - repository evidence not found for a dedicated vulnerability scanning module in the repository. Product Technology catalog references `nessus` (vulnerability scanning) and `trend-micro` (endpoint protection/anti-malware) — inferred from catalog only.

---

## 9.5 Security Event Management

- SIEM Integration: TBD - repository evidence not found.
- Security Monitoring: Implied via `security` domain tagging present across `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` (Module Relationships), but no SIEM/security monitoring integration module found.
- Threat Detection: TBD - repository evidence not found.

Security policy compliance is validated programmatically via `validate_vault_policy(policy_name)` in `src/security_vault.py`, which is part of the deployment_flow validation set.

---

# 10. Maintenance Activities

## 10.1 Routine Operational Tasks

| Activity | Frequency |
|----------|----------|
| Health Checks | TBD - repository evidence not found. (Confirmed by evidence: performed via `validate_automation_results`, `validate_platform_observability`) |
| Capacity Review | TBD - repository evidence not found. |
| Patch Review | TBD - repository evidence not found. (`lifecycle-management` domain present across all modules implies patch/lifecycle activity, but cadence undefined) |
| Backup Verification | TBD - repository evidence not found. (Confirmed mechanism: `validate_backup_integrity`, `generate_backup_report`) |

---

## 10.2 Patch Management

The `lifecycle-management` domain is present in every scanned module (`scripts/detect-impact.py`, `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) — see Module Relationships and Technology Mapping. This indicates lifecycle/patch operations are a cross-cutting concern across the platform, executed through `deploy_configuration_baseline` and `execute_platform_workflow`.

- Maintenance Windows: TBD - repository evidence not found.
- Approval Process: Governed by Pull Request workflow evidenced in `scripts/detect-impact.py` (`get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`).
- Testing Requirements: `validate_automation_results(workflow_name)` must return `True` prior to promotion.

Product Technology catalog references `vlcm` (vSphere Lifecycle Manager), `sddc-manager`, and `aria-suite-lifecycle-manager` as platform lifecycle technologies — inferred from catalog.

---

## 10.3 Upgrade Management

TBD - repository evidence not found for explicit upgrade path/version compatibility declarations in repository source.

---

## 10.4 Capacity Management

TBD - repository evidence not found. No capacity scaling logic or threshold configuration was detected in the repository.

---

# 11. Service Requests

## 11.1 Standard Requests

Based on repository function evidence, the following standard requests are supported:

- Service catalog publication (`publish_service_catalog(catalog_name)` — `src/service_broker.py`)
- Platform API registration (`register_platform_api(api_name)` — `src/service_broker.py`)
- Service offering creation (`create_service_offering(service_name)` — `src/service_broker.py`)
- API subscription validation (`validate_api_subscription(subscription_id)` — `src/service_broker.py`)
- Vault namespace creation (`create_vault_namespace(namespace_name)` — `src/security_vault.py`)
- Customer-managed key creation (`create_customer_managed_key(key_name)` — `src/security_vault.py`)

---

## 11.2 Request Fulfilment Process

Request fulfilment follows the deployment_flow order for the Service Broker module:

```
publish_service_catalog(catalog_name)
        │
        ▼
register_platform_api(api_name)
        │
        ▼
create_service_offering(service_name)
        │
        ▼
validate_api_subscription(subscription_id)
```

Ownership of each fulfilment step: TBD - repository evidence not found.

---

# 12. Incident Management

## 12.1 Incident Classification

| Severity | Description |
|----------|----------|
| P1 | TBD - repository evidence not found. (Suggested mapping: `execute_site_failover` failure or `validate_recovery_objectives` failure — service/site-level impact) |
| P2 | TBD - repository evidence not found. (Suggested mapping: `validate_automation_results` or `validate_platform_observability` failure) |
| P3 | TBD - repository evidence not found. (Suggested mapping: `validate_backup_integrity` or `validate_vault_policy` failure) |
| P4 | TBD - repository evidence not found. (Suggested mapping: `validate_api_subscription` failure) |

---

## 12.2 Operational Troubleshooting

Troubleshooting procedures are built directly from repository function chains (function_relationships / deployment_flow).

### 12.2.1 Automation & Configuration Baseline Failure (`src/automation.py`)

Chain: `provision_infrastructure(environment_name)` → `deploy_configuration_baseline(environment_name)` → `validate_automation_results(workflow_name)`

1. Confirm `provision_infrastructure(environment_name)` returned `True`. If `False`, verify target environment inputs.
2. Confirm `deploy_configuration_baseline(environment_name)` applied without error.
3. Run `validate_automation_results(workflow_name)`. If `False`, re-run `execute_platform_workflow(workflow_name)` and re-validate.

### 12.2.2 Deployment Failure (`src/deploy.py`)

Chain: `deploy_network_foundation(region)` → `deploy_kubernetes_platform(cluster_name)` → `deploy_ai_platform(environment)` → `deploy_data_platform(environment)` → `validate_platform_observability(environment)`

1. Verify `deploy_network_foundation(region)` succeeded before proceeding — network is the foundation dependency for Kubernetes, AI and data platform deployment.
2. Verify `deploy_kubernetes_platform(cluster_name)` succeeded before `deploy_ai_platform`/`deploy_data_platform`.
3. If `validate_platform_observability(environment)` returns `False`, treat as a monitoring/logging/observability configuration defect and re-check each prior deployment step in sequence (network → Kubernetes → AI → data).

### 12.2.3 Backup Failure (`src/backup.py`)

Chain: `schedule_backup_job(workload_name)` → `execute_backup(workload_name)` → `validate_backup_integrity(backup_id)` → `generate_backup_report()`

1. Confirm the job was scheduled (`schedule_backup_job`).
2. Confirm `execute_backup(workload_name)` completed successfully.
3. If `validate_backup_integrity(backup_id)` returns `False`, treat the backup as invalid and re-trigger `execute_backup`.
4. Use `generate_backup_report()` output to confirm final backup state.

### 12.2.4 Disaster Recovery / Failover Failure (`src/dr_platform.py`)

Chain: `create_recovery_plan(application_name)` → `execute_site_failover(target_site)` → `validate_recovery_objectives(application_name)` → `generate_dr_readiness_report()`

1. Confirm a recovery plan exists (`create_recovery_plan`) for the affected application prior to failover.
2. If `execute_site_failover(target_site)` fails, do not proceed to validation — escalate per Section 4.3.
3. Run `validate_recovery_objectives(application_name)` post-failover; a `False` result indicates recovery objectives were not met.
4. Use `generate_dr_readiness_report()` to confirm overall DR posture.

### 12.2.5 Vault / Key Management Failure (`src/security_vault.py`)

Chain: `create_vault_namespace(namespace_name)` → `create_customer_managed_key(key_name)` → `rotate_encryption_key(key_name)` → `assign_key_to_service(key_name, service_name)` → `validate_vault_policy(policy_name)`

1. Confirm namespace exists before key creation.
2. Confirm key creation succeeded before rotation/assignment.
3. If `validate_vault_policy(policy_name)` returns `False`, review key assignment (`assign_key_to_service`) and namespace configuration.

### 12.2.6 Service Broker / API Failure (`src/service_broker.py`)

Chain: `publish_service_catalog(catalog_name)` → `register_platform_api(api_name)` → `create_service_offering(service_name)` → `validate_api_subscription(subscription_id)`

1. Confirm catalog publication succeeded before API registration.
2. Confirm API registration succeeded before offering creation.
3. If `validate_api_subscription(subscription_id)` returns `False`, investigate subscription state against the registered API/offering.

---

## 12.3 Known Issues

| Issue | Workaround |
|----------|----------|
| `resolve_capabilities_for_changed_file` docstring/return-type parsing in `scripts/detect-impact.py` was detected via fallback regex parser (AST parse failed) | Review function manually; do not rely solely on automated documentation extraction for this function. |
| `src/backup.py`, `src/dr_platform.py` parsed via fallback regex parser (`ast_failed_regex_fallback`) | Manually verify function signatures and logic before operational reliance. |

---

# 13. Compliance & Audit

## 13.1 Compliance Requirements

TBD - repository evidence not found. No compliance framework references (ISO27001, GDPR, PCI-DSS) were found in the repository.

---

## 13.2 Audit Requirements

- Audit Responsibilities: TBD - repository evidence not found.
- Log Retention: TBD - repository evidence not found.
- Evidence Collection: `generate_backup_report()` (`src/backup.py`) and `generate_dr_readiness_report()` (`src/dr_platform.py`) provide structured dictionary outputs suitable for audit evidence collection.

---

# 14. Operational Readiness Checklist

| Item | Status |
|----------|----------|
| Monitoring Configured | Partial — `validate_platform_observability` exists; thresholds/dashboards TBD |
| Alerting Configured | Not Confirmed — no alerting module found in repository |
| Backup Configured | Confirmed — `src/backup.py` functions present |
| Recovery Tested | Not Confirmed — `generate_dr_readiness_report` exists; test cadence TBD |
| Runbooks Available | Partial — function-level chains documented in Section 12.2; formal runbook documents TBD |
| Ownership Assigned | Not Confirmed — TBD |
| Escalation Defined | Not Confirmed — TBD |
| Documentation Complete | In Progress |

---

# 15. RAID Register

## Risks

| Risk | Impact | Mitigation |
|----------|----------|----------|
| No dedicated alerting module detected in repository | Delayed incident detection | Implement alerting integration tied to `validate_*` function outcomes |
| Two source files (`src/backup.py`, `src/dr_platform.py`) parsed via fallback regex (AST parse failed) | Reduced confidence in automated documentation accuracy for backup/DR logic | Manual code review of `src/backup.py` and `src/dr_platform.py` |
| No numeric RTO/RPO/thresholds defined in repository | Unclear recovery/monitoring expectations | Define and document RTO/RPO and thresholds with Service Owner |

---

## Assumptions

| Assumption | Owner |
|----------|----------|
| Underlying infrastructure technologies (vSphere, NSX-T, vSAN, Aria Suite, Tanzu) referenced in the Product Technology catalog are deployed and operated per vendor documentation | TBD |
| Module docstring author ("Jijeesh Valappil") reflects code authorship, not operational ownership | TBD |

---

## Issues

| Issue | Owner |
|----------|----------|
| No alerting module present in repository | TBD |
| No explicit SLA/SLO/RTO/RPO values defined in repository | TBD |

---

## Dependencies

| Dependency | Owner |
|----------|----------|
| `src/deploy.py` deployment sequence depends on `deploy_network_foundation` succeeding before `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform` | TBD |
| `src/dr_platform.py` DR domain depends on `backup` domain (Module Relationships: `src/dr_platform.py -> backup`) | TBD |
| `src/security_vault.py` supports `api-service-broker`, `automation`, and `kubernetes` domains | TBD |
| `scripts/detect-impact.py` drives documentation/impact workflows across `ai-platform`, `api-service-broker`, `automation`, `compute`, `data-platform`, `lifecycle-management` domains | TBD |

---

# 16. Appendices

## 16.1 Useful Links

| Link | Purpose |
|----------|----------|
| Repository: `jijeeshlearningorg/greenfield-code` (branch `main`) | Source of operational automation modules |

---

## 16.2 Tooling

| Tool | Purpose |
|----------|----------|
| `scripts/detect-impact.py` | Detects changed files and maps them to impacted product capabilities/domains for documentation and change impact analysis |
| `src/automation.py` | Infrastructure provisioning and platform workflow automation |
| `src/backup.py` | Backup scheduling, execution, integrity validation and reporting |
| `src/deploy.py` | Platform deployment (network, Kubernetes, AI, data) and observability validation |
| `src/dr_platform.py` | Disaster recovery planning, failover execution and recovery validation |
| `src/security_vault.py` | Vault namespace, key creation, rotation and policy validation |
| `src/service_broker.py` | Service catalog publication, API registration and subscription validation |
| Aria Suite (`aria-automation`, `aria-orchestrator`, `aria-operations`, `aria-logs`, `aria-network-insight`) | Catalog-referenced automation, monitoring, and logging platform (inferred) |
| `hashicorp-vault` | Catalog-referenced secrets management platform underlying `src/security_vault.py` (inferred) |
| `canopy-enterprise-backup`, `avamar`, `data-domain` | Catalog-referenced backup platform underlying `src/backup.py` (inferred) |
| `srm`, `vsphere-replication` | Catalog-referenced DR platform underlying `src/dr_platform.py` (inferred) |

---

## 16.3 Contacts

| Team | Contact |
|----------|----------|
| Operations Team | TBD - repository evidence not found. |
| Security Team | TBD - repository evidence not found. |
| Support Team | TBD - repository evidence not found. |

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
| DR | Disaster Recovery |
| PR | Pull Request |
