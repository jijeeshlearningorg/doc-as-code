# Architecture Decision Record: Capability-Aligned Module Decomposition and Automated Impact Detection for My Cloud Services Platform

* Status: Proposed
* Owner: Enterprise Architecture Team
* Deciders: Jijeesh Valappil (Repository Author), Platform Engineering, Enterprise Architecture
* Working group: Platform Automation Engineering (owners of `src/` modules and `scripts/detect-impact.py`)
* Creation Date: 2024-02-08
* Last Revisited: 2024-02-08
* Revision: 1.0

## Context and Problem Statement

The `jijeeshlearningorg/greenfield-code` repository (product: `my-cloud-platform`, architecture product: `My Cloud Services`) implements a set of Python modules that each represent a distinct operational domain of the VMware Cloud Foundation-based cloud platform: `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py`. In addition, `scripts/detect-impact.py` provides a change-impact detection mechanism that maps changed repository files to product capabilities (via `resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, and `build_doc_request`) to drive automated documentation and governance workflows.

A decision is required on:
1. Whether to structure platform automation logic as capability-aligned, single-domain modules versus a monolithic module.
2. How change impact should be assessed and propagated to capability owners and documentation pipelines.
3. How the module boundaries should map to the published Product Capabilities (`automation`, `backup`, `disaster-recovery`, `security`, `api-service-broker`, `compute`, `networking`, `kubernetes`, `ai-platform`, `data-platform`, `storage`, `lifecycle-management`, `observability`).

This decision is being made now because the repository already exhibits a clear, evidence-based module-to-capability mapping (see Module Relationships and Capability Mapping) and an operational deployment/validation flow (see Deployment Flow) that should be formally recognized as the architecture baseline rather than left as an implicit convention.

## Assumptions (Optional)

- The functions in `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py` (all returning `bool` or `dict`) are orchestration entry points that, in the target platform, invoke underlying VMware Cloud Foundation technologies listed in the Product Technologies catalog (e.g., `aria-automation`, `sddc-manager`, `hashicorp-vault`, `srm`, `vsphere-replication`, `service-broker`). This is **inferred** — the source code itself does not import these technologies directly; only `logging` is imported (per Module Relationships: `src/automation.py -> logging`, `src/deploy.py -> logging`, `src/security_vault.py -> logging`, `src/service_broker.py -> logging`).
- `scripts/detect-impact.py` is assumed to run as part of a CI/CD or documentation-generation pipeline given its functions (`get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`, `write_json`) — inferred from function naming, not from explicit CI configuration evidence in the scanned files.
- Each source module is assumed to represent a bounded, capability-owned automation surface rather than a shared/shared-kernel library, based on the one-to-many `supports_domain` relationships observed per file.

## Constraints (Optional)

- `src/backup.py`, `src/dr_platform.py`, and `scripts/detect-impact.py` were parsed via `ast_failed_regex_fallback`, indicating these files could not be fully parsed by the standard AST parser and required a regex-based fallback. This constrains the reliability of fully automated static analysis for these three files and should be treated as a documentation/tooling constraint, not a runtime defect.
- No classes were detected in the repository (`Classes detected: 0`); all logic is function-based. Any architectural decision must operate within this procedural, function-oriented structure rather than assuming object-oriented abstractions.
- Only 4 imports were detected across the entire repository, and only `logging` is referenced in Module Relationships. Decisions cannot assume any additional runtime dependencies (e.g., SDKs for `vsphere`, `nsx-t`, `aria-automation`) are present in code — these exist only in the product technology catalog, not in source.
- `src/deploy.py` supports nine domains simultaneously (`ai-platform`, `api-service-broker`, `compute`, `data-platform`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security`), which is a repository-evidenced constraint on module cohesion that must be acknowledged in the decision rather than idealized away.

## Considered Options

1. **Monolithic single-module design** — Combine all automation, backup, deployment, DR, security, and service-broker logic into a single file/module.
2. **Capability-aligned module decomposition (as currently implemented)** — Retain discrete modules (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`), each mapped to one or more product capabilities via `supports_domain` relationships, with a dedicated cross-cutting `detect-impact.py` for capability-impact resolution.
3. **Per-domain impact detection scripts** — Replace the single `scripts/detect-impact.py` with one impact-detection script per module/domain.
4. **Centralized impact detection (as currently implemented)** — Retain a single `scripts/detect-impact.py` that resolves capabilities for any changed file via a configurable `path_mapping`, used by `resolve_capabilities_for_changed_file` and `build_impacted_capabilities`.

## Proposed Design (Optional)

Formalize the repository's existing structure as the architecture baseline:

- **Domain modules** remain capability-scoped:
  - `src/automation.py` → capability `automation` (functions: `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`)
  - `src/backup.py` → capability `backup` (functions: `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`)
  - `src/deploy.py` → multi-capability deployment orchestration spanning `compute`, `networking`, `kubernetes`, `ai-platform`, `data-platform` (functions: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`)
  - `src/dr_platform.py` → capabilities `backup` and `disaster-recovery` (functions: `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`)
  - `src/security_vault.py` → capability `security` (functions: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
  - `src/service_broker.py` → capability `api-service-broker` (functions: `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`)
- **Cross-cutting impact detection** remains centralized in `scripts/detect-impact.py`, using `resolve_capabilities_for_changed_file` and `build_impacted_capabilities` against a configurable path-to-capability mapping, feeding `build_doc_request` and `write_json` for downstream documentation/ADR automation.
- **Operational sequencing**, derived from Deployment Flow, is preserved as the execution order per module:
  - Automation: `provision_infrastructure` → `deploy_configuration_baseline` → `validate_automation_results`
  - Backup: `schedule_backup_job` → `execute_backup` → `validate_backup_integrity` → `generate_backup_report`
  - Deploy: `deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability`
  - DR: `create_recovery_plan` → `validate_recovery_objectives`
  - Security Vault: `validate_vault_policy` (governance checkpoint)
  - Service Broker: `publish_service_catalog` → `register_platform_api` → `validate_api_subscription`

## Decision Outcome

Adopt **Option 2 and Option 4**: retain and formalize the capability-aligned module decomposition (`src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) as the architectural baseline, with `scripts/detect-impact.py` serving as the single, centralized capability-impact resolution mechanism for the repository.

This decision is justified directly by repository evidence:
- Module Relationships show each source file explicitly declares one or more `supports_domain` relationships aligned to Product Capabilities (e.g., `src/security_vault.py -> security`, `src/backup.py -> backup`, `src/dr_platform.py -> disaster-recovery`).
- Capability Mapping confirms a direct file-to-capability association for `automation.py` (`automation`), `backup.py` (`backup`), and `security_vault.py` (`security`), reinforcing that decomposition boundaries are already capability-driven rather than incidental.
- Function Relationships and Deployment Flow demonstrate that each module encapsulates a self-contained operational lifecycle (provision/deploy/validate, backup/validate, recovery/validate, publish/register/validate), supporting independent evolution per capability.
- A single centralized `scripts/detect-impact.py`, rather than per-domain scripts, avoids duplicating path-to-capability resolution logic (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`) and keeps the impact-detection contract in one place, consistent with the current implementation.

The multi-domain breadth of `src/deploy.py` (spanning `ai-platform`, `api-service-broker`, `compute`, `data-platform`, `kubernetes`, `networking`, plus cross-cutting `observability`, `security`, `lifecycle-management`) and `src/dr_platform.py` (spanning `backup` and `disaster-recovery`) is accepted as-is; no refactoring of these modules is recommended, as no repository evidence indicates a functional or maintainability problem with the current grouping.

## Related Artifacts (Optional)

- `README.md` — repository entry point documentation (minimal, 3 lines; no additional architectural content detected).
- `scripts/detect-impact.py` — capability impact detection driving downstream documentation/ADR generation (`build_doc_request`, `write_json`).
- Product Capability Catalog — defines `automation`, `backup`, `disaster-recovery`, `security`, `api-service-broker`, `compute`, `networking`, `kubernetes`, `containers`, `data-platform` boundaries referenced by Module Relationships and Capability Mapping.
- Product Technology Catalog — defines the underlying platform technologies (`vsphere`, `nsx-t`, `aria-automation`, `hashicorp-vault`, `srm`, `service-broker`, etc.) presumed (inferred) to back the orchestration functions in `src/*.py`, though not directly imported in the scanned source.

## Comments (Optional)

TBD - repository evidence not found.
