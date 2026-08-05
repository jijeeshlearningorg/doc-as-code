# Architecture Decision Record

* Status: Proposed
* Owner: Enterprise Architecture Team (inferred from repository authorship metadata)
* Deciders: Platform Engineering, Enterprise Architecture, Security Engineering (inferred from module domain ownership)
* Working group: Contributors to `jijeeshlearningorg/greenfield-code` (owner attribution: "Jijeesh Valappil" per module docstrings)
* Creation Date: 2024-02-08
* Last Revisited: 2024-02-08
* Revision: 1

## Context and Problem Statement

The `my-cloud-platform` repository (`jijeeshlearningorg/greenfield-code`, branch `main`) implements a set of automation modules that orchestrate My Cloud Services platform capabilities. Repository scanning identified 8 source files and 41 functions organized into distinct, single-purpose Python modules under `src/` plus a repository-level impact detection script under `scripts/`.

Each module maps to one or more architecture domains as follows (per `module_relationships` and `technology_mapping`):

- `src/automation.py` → `automation`, `lifecycle-management`, `observability`, `security` (functions: `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`)
- `src/backup.py` → `backup`, `lifecycle-management`, `observability`, `security`, `storage` (functions: `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`)
- `src/deploy.py` → `ai-platform`, `api-service-broker`, `compute`, `data-platform`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security` (functions: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`)
- `src/dr_platform.py` → `ai-platform`, `backup`, `disaster-recovery`, `lifecycle-management`, `observability`, `security` (functions: `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`)
- `src/security_vault.py` → `api-service-broker`, `automation`, `kubernetes`, `lifecycle-management`, `observability`, `security` (functions: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
- `src/service_broker.py` → `api-service-broker`, `lifecycle-management`, `observability`, `security` (functions: `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`)
- `scripts/detect-impact.py` → `ai-platform`, `api-service-broker`, `automation`, `compute`, `data-platform`, `lifecycle-management` (functions: `read_yaml`, `read_changed_files`, `normalize_path`, `unique_sorted`, `get_repository_name`, `get_repository_full_name`, `get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`, `resolve_product`, `resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request`, `write_json`, `main`)

The problem to be decided is how to structure and evolve this codebase so that platform capabilities (compute, networking, storage, automation, security, backup, disaster-recovery, kubernetes, api-service-broker, ai-platform, data-platform, lifecycle-management, observability) remain independently deployable, traceable to source, and automatically assessable for change impact as the repository grows. This decision is being made now because the repository already exhibits a clear capability-to-module decomposition and an explicit impact-detection mechanism (`scripts/detect-impact.py`) that must be formally recognized and preserved as the architecture evolves.

## Assumptions (Optional)

- The module-to-domain mappings captured in `module_relationships`, `technology_mapping`, and `capability_mapping` accurately represent the current and intended capability boundaries of the platform (inferred from static analysis of the repository, not from a separate design document).
- `scripts/detect-impact.py` is the authoritative mechanism for determining which product capabilities are impacted by a given code change, based on `resolve_capabilities_for_changed_file` and `build_impacted_capabilities`.
- The product capability catalog (`compute`, `storage`, `networking`, `automation`, `monitoring`, `security`, `disaster-recovery`, `backup`, `containers`, `multi-tenancy`, `lifecycle-management`, `public-cloud-integration`, `reporting`, `api-service-broker`) and the technology catalog (vSphere, ESXi, vCenter, vSAN, NSX-T, Aria Suite components, Tanzu, SDDC Manager, vLCM, Trend Micro, Nessus, HashiCorp Vault, Canopy Enterprise Backup, Avamar, Data Domain, SRM, vSphere Replication, HCX, VMC, Service Broker) represent the platform this codebase automates, even though no direct technology-specific imports were detected in the scanned source files (`Detected Technologies:` is empty in the Architecture Summary; only a `logging` import was found in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, and `src/service_broker.py`).
- Functions such as `create_customer_managed_key`, `rotate_encryption_key`, and `assign_key_to_service` in `src/security_vault.py` are inferred to correspond to the `hashicorp-vault` capability in the technology catalog, but no direct library import evidence exists in the scanned code.

## Constraints (Optional)

- Each module currently supports multiple architecture domains simultaneously (e.g., `src/deploy.py` spans 9 domains, `src/dr_platform.py` spans 6 domains). Any decomposition decision must preserve this multiplicity rather than force artificial single-domain boundaries not present in the code.
- `capability_mapping` explicitly ties only three modules to formal product capabilities: `src/automation.py` → `automation`, `src/backup.py` → `backup`, `src/security_vault.py` → `security`. Other modules (`src/deploy.py`, `src/dr_platform.py`, `src/service_broker.py`, `scripts/detect-impact.py`) have domain associations but no explicit capability_mapping entry, and this gap must be treated as a known constraint rather than resolved by invention.
- `deployment_flow` shows only intra-module operational sequences (e.g., `provision_infrastructure` → `deploy_configuration_baseline` → `validate_automation_results` in `src/automation.py`; `deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability` in `src/deploy.py`; `create_recovery_plan` → `validate_recovery_objectives` in `src/dr_platform.py`; `publish_service_catalog` → `register_platform_api` → `validate_api_subscription` in `src/service_broker.py`). No cross-module function call evidence exists in `function_relationships`, so no direct module-to-module orchestration dependency can be assumed.
- No CPU, memory, storage, port, firewall, SLA, SLO, RPO, RTO, or capacity values are present anywhere in the repository evidence; none are introduced in this ADR.

## Considered Options

1. **Preserve current capability-aligned module decomposition** (`src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`) with `scripts/detect-impact.py` as the change-impact resolver.
2. **Consolidate all platform automation into a single monolithic module**, removing the per-capability file boundaries currently observed.
3. **Split each module further into one file per capability domain** (e.g., separating `src/deploy.py`'s `ai-platform`, `kubernetes`, `data-platform`, and `networking` responsibilities into four dedicated files).
4. **Introduce a shared orchestration layer** that explicitly calls across `src/automation.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, and `src/service_broker.py`, where none currently exists in `function_relationships`.

## Proposed Design (Optional)

Retain the existing repository structure as the architectural baseline:

- `scripts/detect-impact.py` remains the single entry point for resolving changed files to impacted capabilities (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request`), driving documentation and impact-analysis workflows (`read_changed_files` → `resolve_capabilities_for_changed_file` → `build_impacted_capabilities` → `build_doc_request` → `write_json`).
- `src/automation.py` continues to own the `automation` capability lifecycle: `provision_infrastructure` → `deploy_configuration_baseline` → `validate_automation_results`, supporting `automation`, `lifecycle-management`, `observability`, and `security` domains.
- `src/backup.py` continues to own the `backup` capability lifecycle: `schedule_backup_job` → `execute_backup` → `validate_backup_integrity` → `generate_backup_report`, supporting `backup`, `storage`, `lifecycle-management`, `observability`, and `security` domains.
- `src/deploy.py` continues to own multi-domain platform deployment: `deploy_network_foundation` → `deploy_kubernetes_platform` → `deploy_ai_platform` → `deploy_data_platform` → `validate_platform_observability`, spanning `compute`, `networking`, `kubernetes`, `ai-platform`, `data-platform`, `api-service-broker`, `lifecycle-management`, `observability`, and `security`.
- `src/dr_platform.py` continues to own the `disaster-recovery` capability lifecycle: `create_recovery_plan` → `execute_site_failover` and `validate_recovery_objectives` → `generate_dr_readiness_report`, supporting `backup`, `ai-platform`, `lifecycle-management`, `observability`, and `security`.
- `src/security_vault.py` continues to own the `security` capability lifecycle (key/secrets management functions consistent with the `hashicorp-vault` technology-catalog entry): `create_vault_namespace` → `create_customer_managed_key` → `rotate_encryption_key` / `assign_key_to_service` → `validate_vault_policy`, supporting `api-service-broker`, `automation`, `kubernetes`, `lifecycle-management`, and `observability`.
- `src/service_broker.py` continues to own the `api-service-broker` capability lifecycle: `publish_service_catalog` → `register_platform_api` → `create_service_offering` → `validate_api_subscription`, supporting `lifecycle-management`, `observability`, and `security`.

No cross-module function calls are introduced; each module remains independently invokable, matching current `function_relationships` evidence.

## Decision Outcome

**Option 1 is selected: preserve the current capability-aligned module decomposition.**

The repository evidence demonstrates that each source file already maps cleanly to specific product capabilities and architecture domains via `module_relationships`, `technology_mapping`, and `capability_mapping`. `deployment_flow` confirms coherent intra-module operational sequences (provision/deploy/backup/recovery/validate/publish/register patterns) without any evidence of required cross-module orchestration. Consolidating modules (Option 2) would discard this evidenced separation of concerns; further splitting (Option 3) is not justified because no repository evidence shows internal coupling problems within `src/deploy.py` or other multi-domain modules that would require finer decomposition; introducing a new orchestration layer (Option 4) is not supported because `function_relationships` shows no existing cross-module calls to build upon, and rule 31 prohibits recommending refactoring absent such evidence.

`scripts/detect-impact.py` is retained and formally recognized as the architectural mechanism for determining capability impact of code changes, and its output (`build_doc_request`) should continue to drive downstream documentation and review processes for any future changes to `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, or `src/service_broker.py`.

## Related Artifacts (Optional)

- `scripts/detect-impact.py` — capability impact detection script
- `src/automation.py` — automation and lifecycle-management module
- `src/backup.py` — backup and storage module
- `src/deploy.py` — compute, networking, kubernetes, ai-platform, data-platform module
- `src/dr_platform.py` — disaster-recovery module
- `src/security_vault.py` — security/secrets management module
- `src/service_broker.py` — api-service-broker module
- Product capability catalog (My Cloud Services)
- Product technology catalog (My Cloud Services)

## Comments (Optional)

TBD - repository evidence not found.
