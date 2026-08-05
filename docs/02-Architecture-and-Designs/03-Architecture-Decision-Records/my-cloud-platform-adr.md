# Architecture Decision Record: Capability-Aligned Modular Automation Architecture for My Cloud Services Platform

* **Status:** Proposed
* **Owner:** Enterprise Architecture Team
* **Deciders:** Product Owner (My Cloud Services), Platform Engineering Lead, Security Architecture Lead, DevOps/Automation Lead
* **Working group:** Platform Automation Engineering (owners of `src/automation.py`, `src/deploy.py`, `src/backup.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`), CI/CD Tooling Team (owner of `scripts/detect-impact.py`)
* **Creation Date:** 2024-02-08
* **Last Revisited:** 2024-02-08
* **Revision:** 1.0

## Context and Problem Statement

The `my-cloud-platform` repository (`jijeeshlearningorg/greenfield-code`, branch `main`) is the greenfield codebase for **My Cloud Services**, a VMware Cloud Foundation-based private/hybrid cloud platform spanning **13 detected architecture domains**: `ai-platform`, `api-service-broker`, `automation`, `backup`, `compute`, `data-platform`, `disaster-recovery`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security`, and `storage`.

Repository scanning (8 files, 41 functions, 0 classes) shows the codebase is currently organized as a small set of **flat, function-based Python modules**, each of which spans multiple architecture domains simultaneously rather than being isolated to a single capability:

- `src/automation.py` — `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results` — supports `automation`, `lifecycle-management`, `observability`, `security`.
- `src/backup.py` — `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report` — supports `backup`, `lifecycle-management`, `observability`, `security`, `storage`.
- `src/deploy.py` — `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability` — supports `ai-platform`, `api-service-broker`, `compute`, `data-platform`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security`.
- `src/dr_platform.py` — `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report` — supports `ai-platform`, `backup`, `disaster-recovery`, `lifecycle-management`, `observability`, `security`.
- `src/security_vault.py` — `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy` — supports `api-service-broker`, `automation`, `kubernetes`, `lifecycle-management`, `observability`, `security`.
- `src/service_broker.py` — `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription` — supports `api-service-broker`, `lifecycle-management`, `observability`, `security`.

In addition, `scripts/detect-impact.py` (351 lines, regex-fallback parsed) implements a **capability-impact detection utility** (`resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request`, `resolve_product`, `write_json`, `main`) that maps changed source files to product capabilities (`ai-platform`, `api-service-broker`, `automation`, `compute`, `data-platform`, `lifecycle-management`) for downstream automated documentation and architecture-impact governance.

The problem: as the platform grows across 13 domains and 26 underlying VMware/Aria/Tanzu technologies (`vsphere`, `esxi`, `vcenter`, `vsan`, `nsx-t`, `aria-automation`, `aria-orchestrator`, `aria-operations`, `aria-logs`, `aria-network-insight`, `tanzu-kubernetes-grid`, `tanzu-mission-control`, `sddc-manager`, `vlcm`, `aria-suite-lifecycle-manager`, `trend-micro`, `nessus`, `hashicorp-vault`, `canopy-enterprise-backup`, `avamar`, `data-domain`, `srm`, `vsphere-replication`, `hcx`, `vmc`, `service-broker`), there is **no formal architectural boundary** between capability domains inside the codebase. Cross-cutting concerns (`security`, `observability`, `lifecycle-management`) are duplicated informally across nearly every module rather than being centralized. This creates a risk of architectural drift as the platform scales, and makes automated impact analysis (via `scripts/detect-impact.py`) the *only* current mechanism for understanding which capabilities are affected by a given change.

A decision is needed now — at the greenfield stage — on how the codebase should be structured going forward: as capability-aligned modules with embedded cross-cutting concerns (current state), or as a layered/domain-driven architecture with explicit shared cross-cutting services, and whether automated capability-impact detection should be formalized as a governing architectural control.

## Assumptions (Optional)

- The product `My Cloud Services` (product code `my-cloud-platform`) is built on VMware Cloud Foundation-aligned technologies (`vsphere`, `esxi`, `vcenter`, `vsan`, `nsx-t`, `sddc-manager`, `vlcm`) as declared in the product technology catalog. *(inferred from product metadata, not directly observable in scanned source files, which contain no explicit technology imports beyond `logging`)*.
- The current repository (`greenfield-code`) represents an early-stage/reference implementation of platform automation logic rather than the full production codebase, given the small file count (8 files, 41 functions, 0 classes) relative to the breadth of declared domains and technologies.
- `scripts/detect-impact.py` is intended to run in a CI/CD pipeline (evidenced by functions `get_repository_name`, `get_repository_full_name`, `get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`) to support automated architecture documentation generation on pull requests. *(inferred)*
- The absence of detected classes (0 classes across the repository) indicates the platform automation layer currently favors a **procedural, function-based style** over object-oriented domain modeling.
- Multi-tenancy, public-cloud-integration, and reporting capabilities (declared in the product capability catalog) are not yet represented in source code and are assumed to be planned for future implementation phases.

## Constraints (Optional)

- The platform must continue to align with the declared **Product Capabilities** catalog (`compute`, `storage`, `networking`, `automation`, `monitoring`, `security`, `disaster-recovery`, `backup`, `containers`, `multi-tenancy`, `lifecycle-management`, `public-cloud-integration`, `reporting`, `api-service-broker`) even though only a subset (`automation`, `backup`, `security`) currently has a direct 1:1 capability mapping in the `Capability Mapping` evidence.
- Cross-cutting concerns of `security`, `observability`, and `lifecycle-management` appear in **every single automation module** (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`), indicating these must be treated as first-class architectural concerns rather than incidental module behavior.
- `scripts/detect-impact.py` uses a **regex fallback parser** (AST parsing failed) — any architectural decision relying on this tool's output must account for reduced parsing fidelity until the tooling is hardened.
- No class-based domain models exist yet (0 classes detected); any architectural refactoring must be introduced without breaking existing function signatures consumed elsewhere (e.g., `provision_infrastructure(environment_name)`, `execute_backup(workload_name)`, `create_recovery_plan(application_name)`).
- Secrets and encryption key management responsibilities are concentrated in `src/security_vault.py` (`create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`), implying dependency on `hashicorp-vault` per the technology catalog; this is an architectural coupling point that constrains how security capability can be decomposed.

## Considered Options

**Option A — Retain current flat, capability-tagged module structure (status quo)**
Keep `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` as-is, each spanning multiple domains, relying on `scripts/detect-impact.py` for downstream governance/documentation.

**Option B — Adopt a Domain-Driven, Capability-Aligned Modular Architecture with centralized cross-cutting services**
Refactor toward explicit domain boundaries per the Product Capabilities catalog (e.g., dedicated modules/packages for `compute`, `networking`, `containers`, `disaster-recovery`, `backup`, `api-service-broker`), extracting shared `security`, `observability`, and `lifecycle-management` behavior (currently duplicated across `automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`) into common cross-cutting libraries/services.

**Option C — Monolithic single-service architecture**
Consolidate all platform automation logic (currently split across 6 source files) into a single deployable service/module, reducing file count but increasing coupling and reducing independent deployability of capabilities such as `disaster-recovery` (`src/dr_platform.py`) vs `security` (`src/security_vault.py`).

**Option D — Formalize automated capability-impact governance as an architecture control**
Elevate `scripts/detect-impact.py` from a documentation-generation utility to a **mandatory CI/CD architecture gate**, requiring every pull request to declare/validate impacted capabilities (`ai-platform`, `api-service-broker`, `automation`, `compute`, `data-platform`, `lifecycle-management`, and extended to all 13 detected domains) before merge, and to harden its parsing (currently regex-fallback) to full AST-based analysis.

## Proposed Design (Optional)

The proposed direction combines **Option B** (capability-aligned modular refactor) with **Option D** (formalized impact governance), while preserving backward compatibility with existing function signatures:

1. **Capability boundary alignment**: Reorganize `src/` into capability-scoped packages mapped explicitly to the Product Capabilities catalog:
   - `automation/` (from `src/automation.py`: `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`)
   - `backup/` (from `src/backup.py`: `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`)
   - `deploy/` split by sub-domain (from `src/deploy.py`: `deploy_network_foundation` → networking, `deploy_kubernetes_platform` → kubernetes, `deploy_ai_platform` → ai-platform, `deploy_data_platform` → data-platform)
   - `disaster_recovery/` (from `src/dr_platform.py`: `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`)
   - `security/` (from `src/security_vault.py`: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
   - `service_broker/` (from `src/service_broker.py`: `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`)

2. **Cross-cutting shared layer**: Extract a shared `observability`/`security`/`lifecycle-management` utility layer, since these three domains recur across every scanned module (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`), each currently importing `logging` independently.

3. **CI/CD governance hardening**: Upgrade `scripts/detect-impact.py` from regex-fallback parsing to full AST-based parsing (mirroring the success achieved in `src/automation.py`, `src/deploy.py`, `src/security_vault.py`, `src/service_broker.py`, which parsed via `ast_success`), and wire `build_impacted_capabilities` / `build_doc_request` into a mandatory PR check.

## Decision Outcome

**Decision:** Adopt **Option B (Capability-Aligned Modular Architecture with centralized cross-cutting services)** combined with **Option D (formalized automated capability-impact governance)**.

The platform will evolve from the current flat, multi-domain module structure toward explicit capability-aligned boundaries consistent with the declared Product Capabilities catalog, while retaining and hardening `scripts/detect-impact.py` as the authoritative mechanism for architecture impact detection and documentation generation. This decision preserves the existing function-level contracts (e.g., `execute_backup(workload_name)`, `create_recovery_plan(application_name)`, `rotate_encryption_key(key_name)`) to minimize migration risk while introducing clearer domain ownership.

### Consequences

**Positive:**
- Clearer ownership and change-impact boundaries per capability (`compute`, `storage`, `networking`, `backup`, `disaster-recovery`, `security`, `api-service-broker`, etc.), reducing risk of unintended cross-domain regressions.
- Reduced duplication of `security`, `observability`, and `lifecycle-management` logic currently repeated across `automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`.
- Formalized use of `scripts/detect-impact.py` improves architecture traceability and supports automated documentation generation for the `my-cloud-platform` product.
- Aligns codebase structure with the already-published Product Capabilities and Technology catalogs, improving consistency between documentation and implementation.

**Negative / Risks:**
- Refactoring effort required to decompose multi-domain modules (e.g., `src/deploy.py` currently spans 9 domains) into capability-scoped packages.
- `scripts/detect-impact.py` currently relies on regex-fallback parsing (AST parse failed); hardening to full AST parsing is a prerequisite dependency and introduces near-term tooling work.
- Introducing a mandatory CI/CD architecture gate may slow initial PR velocity until the capability-mapping configuration is fully validated.
- Since 0 classes are currently detected in the repository, moving to clearer domain boundaries may still remain function-based rather than object-oriented, which could limit long-term extensibility for capabilities such as `multi-tenancy` and `public-cloud-integration` that are not yet implemented in code.

**Neutral:**
- No change to underlying VMware Cloud Foundation technology choices (`vsphere`, `nsx-t`, `vsan`, `aria-automation`, etc.) — this ADR governs code/module architecture, not infrastructure technology selection.

## Related Artifacts (Optional)

- `scripts/detect-impact.py` — capability/domain impact detection tooling referenced by this decision.
- `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` — subject modules for capability-aligned refactor.
- Product Capability Catalog — `compute`, `storage`, `networking`, `automation`, `monitoring`, `security`, `disaster-recovery`, `backup`, `containers`, `multi-tenancy`, `lifecycle-management`, `public-cloud-integration`, `reporting`, `api-service-broker`.
- Product Technology Catalog — `vsphere`, `esxi`, `vcenter`, `vsan`, `nsx-t`, `aria-automation`, `aria-orchestrator`, `aria-operations`, `aria-logs`, `aria-network-insight`, `tanzu-kubernetes-grid`, `tanzu-mission-control`, `sddc-manager`, `vlcm`, `aria-suite-lifecycle-manager`, `trend-micro`, `nessus`, `hashicorp-vault`, `canopy-enterprise-backup`, `avamar`, `data-domain`, `srm`, `vsphere-replication`, `hcx`, `vmc`, `service-broker`.
- Future ADRs anticipated for: `multi-tenancy` implementation, `public-cloud-integration` (VMC-based), and `reporting` capability, none of which currently have corresponding source files in this repository.

## Comments (Optional)

- Repository parse quality note: `scripts/detect-impact.py`, `src/backup.py`, and `src/dr_platform.py` fell back to regex-based parsing rather than full AST parsing; this should be tracked as a technical-debt item impacting confidence in future automated architecture analyses.
- Working group to confirm whether `src/deploy.py`'s multi-domain scope (`ai-platform`, `api-service-broker`, `compute`, `data-platform`, `kubernetes`, `lifecycle-management`, `networking`, `observability`, `security`) should be split in the first refactor iteration or deferred to a follow-up ADR given its central role in platform deployment (`deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`).
