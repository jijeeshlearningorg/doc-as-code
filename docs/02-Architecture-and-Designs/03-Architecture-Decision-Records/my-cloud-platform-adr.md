# Architecture Decision Record: Domain-Aligned Modular Automation Architecture with Automated Capability-Impact Traceability for My Cloud Services

* **Status:** Proposed
* **Owner:** Enterprise Architecture Team
* **Deciders:** Platform Engineering Lead, Enterprise Architect, Cloud Operations Lead, Security Architecture Lead
* **Working Group:** `jijeeshlearningorg/greenfield-code` maintainers (Platform Automation Team) — responsible for implementing and maintaining `src/*.py` domain modules and `scripts/detect-impact.py`
* **Creation Date:** 2024-02-08
* **Last Revisited:** 2024-02-08
* **Revision:** 1.0

---

## Context and Problem Statement

`My Cloud Services` (product: `my-cloud-platform`) is a VMware-based enterprise cloud platform spanning compute (vSphere/ESXi), storage (vSAN), networking (NSX-T), automation (Aria Automation/Orchestrator), Kubernetes (Tanzu), disaster recovery (SRM/vSphere Replication/HCX), backup (Avamar/Data Domain/Canopy), security (HashiCorp Vault/Trend Micro/Nessus), and public cloud integration (VMC).

The repository (`greenfield-code`) implements this platform's automation and orchestration surface as a set of discrete Python modules rather than a monolithic script or a class-based framework. Repository scanning confirms:

- **Zero classes detected** across 8 files despite 41 functions — indicating a deliberate function-oriented, procedural design rather than object-oriented service abstractions.
- Each source file maps to a distinct product capability domain:
  - `src/automation.py` → `automation`, `lifecycle-management`, `observability`, `security` (functions: `provision_infrastructure`, `execute_platform_workflow`, `deploy_configuration_baseline`, `validate_automation_results`)
  - `src/backup.py` → `backup`, `storage`, `lifecycle-management`, `observability`, `security` (functions: `schedule_backup_job`, `execute_backup`, `validate_backup_integrity`, `generate_backup_report`)
  - `src/deploy.py` → `compute`, `networking`, `kubernetes`, `ai-platform`, `data-platform`, `api-service-broker`, `observability`, `security` (functions: `deploy_network_foundation`, `deploy_kubernetes_platform`, `deploy_ai_platform`, `deploy_data_platform`, `validate_platform_observability`)
  - `src/dr_platform.py` → `disaster-recovery`, `backup`, `ai-platform`, `observability`, `security` (functions: `create_recovery_plan`, `execute_site_failover`, `validate_recovery_objectives`, `generate_dr_readiness_report`)
  - `src/security_vault.py` → `security`, `api-service-broker`, `automation`, `kubernetes` (functions: `create_vault_namespace`, `create_customer_managed_key`, `rotate_encryption_key`, `assign_key_to_service`, `validate_vault_policy`)
  - `src/service_broker.py` → `api-service-broker`, `lifecycle-management`, `observability`, `security` (functions: `publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`)
- A dedicated CI/build-time script, `scripts/detect-impact.py` (351 lines, regex-fallback parsed), implements a **capability-impact detection pipeline**: `read_changed_files`, `resolve_capabilities_for_changed_file`, `build_impacted_capabilities`, `build_doc_request`, and `write_json`, driven by a path-to-capability mapping (`path_mapping`) and PR metadata (`get_repository_name`, `get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`).

The architectural problem this ADR addresses is: **how should the platform's automation codebase be structured so that (a) each product capability has clear, isolated ownership of its automation logic, and (b) changes to that code are automatically and traceably linked to the capabilities and documentation they affect**, given a 14-capability, 26-technology enterprise platform with cross-cutting concerns (security, observability, lifecycle-management) touching almost every module.

This decision must be made now because the repository is greenfield — no established pattern yet governs how future capability modules (e.g., additional AI-platform or data-platform services) should be added, and no automated mechanism currently exists to prevent capability/documentation drift as the codebase grows.

## Assumptions (Optional)

- The current absence of classes (0 detected) is an intentional architectural choice for this stage of the platform, not a parsing artifact — confirmed by `ast_success` parse status on 3 of 8 files (`automation.py`, `deploy.py`, `security_vault.py`, `service_broker.py`). *(Inferred: procedural style is deliberate rather than accidental.)*
- `scripts/detect-impact.py` is intended to run in a CI/CD pipeline (e.g., on pull requests) given its reliance on `get_pull_request_number`, `get_pull_request_title`, `get_pull_request_url`, and `get_repository_full_name`. *(Inferred from function signatures; no pipeline YAML was present in the scanned files.)*
- The product catalog's 14 declared capabilities are the authoritative taxonomy against which all source files and future modules must be mapped.
- Cross-cutting domains (`security`, `observability`, `lifecycle-management`) are expected to appear in most modules and are not evidence of poor separation of concerns, but of legitimate shared platform concerns.
- The regex-fallback parsing on `scripts/detect-impact.py`, `src/backup.py`, and `src/dr_platform.py` indicates these files may use syntax or constructs not fully supported by the AST parser (e.g., complex type hints or multi-line signatures) — a code-quality/tooling risk rather than a functional defect. *(Inferred.)*
- No automated test suite was detected in the 8 scanned files; validation functions (`validate_automation_results`, `validate_backup_integrity`, `validate_recovery_objectives`, `validate_vault_policy`, `validate_api_subscription`, `validate_platform_observability`) serve as the primary in-code verification pattern.

## Constraints (Optional)

- The platform is built exclusively on the VMware SDDC stack (vSphere, vSAN, NSX-T, Aria Suite, Tanzu, SDDC Manager) — any architectural pattern chosen must remain compatible with VMware Aria Automation/Orchestrator workflow invocation patterns, since `execute_platform_workflow` and `deploy_configuration_baseline` in `src/automation.py` imply orchestration-workflow delegation rather than direct infrastructure API calls.
- Security-sensitive operations (`create_customer_managed_key`, `rotate_encryption_key` in `src/security_vault.py`) must remain isolated from general automation/deploy modules to satisfy compliance and least-privilege boundaries implied by the `hashicorp-vault` technology dependency.
- The capability-to-file mapping used by `resolve_capabilities_for_changed_file` must be kept synchronized with the product/capability catalog; any new source file must be registered in `path_mapping` or it will not be traceable to a capability.
- Only 4 imports were detected repository-wide, suggesting minimal external library dependency at this stage — any architectural direction should avoid introducing heavy framework dependencies prematurely.
- No classes exist today; introducing object-oriented refactoring must be backward-compatible with existing function-based call sites (`deploy_network_foundation`, `deploy_kubernetes_platform`, etc.) if adopted incrementally.

## Considered Options

**Option A — Continue Domain-Aligned Functional Modules per Capability (as-is, formalized)**
Retain the current pattern of one module per capability domain (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`), formalize naming/interface conventions, and mandate that new capabilities (e.g., `data-platform`, `ai-platform` growth) receive dedicated modules rather than being appended to `deploy.py`.

**Option B — Consolidate into a Single Monolithic Orchestration Module**
Merge all automation logic into a single file to reduce file-count overhead. Rejected direction, evaluated for completeness.

**Option C — Refactor to Class-Based Service Abstractions (introduce OO design)**
Introduce classes (e.g., `AutomationService`, `BackupService`, `DRPlatformService`) wrapping existing functions, enabling dependency injection, state management, and polymorphic extension for multi-hyperscaler (`vmc`) support.

**Option D — Adopt Domain Modules + Formalize Automated Capability-Impact Traceability (Option A + detect-impact.py as a governance gate)**
Combine Option A with elevating `scripts/detect-impact.py` from a utility script to a mandatory CI gate that (1) blocks merges lacking capability mapping, (2) auto-generates documentation-impact requests (`build_doc_request`) on every PR, and (3) fails builds if a changed file resolves to zero capabilities.

## Proposed Design (Optional)

Adopt **Option D**. Architecture direction:

1. **Module-per-capability convention**: Each capability domain (`compute`, `networking`, `automation`, `backup`, `disaster-recovery`, `security`, `containers`, `api-service-broker`, etc.) SHOULD map to one or more dedicated modules under `src/`. Existing modules already conform: `src/deploy.py` (compute/networking/kubernetes/ai-platform/data-platform), `src/backup.py` (backup/storage), `src/dr_platform.py` (disaster-recovery), `src/security_vault.py` (security key management), `src/service_broker.py` (api-service-broker), `src/automation.py` (automation/lifecycle-management).
2. **Validation-function pairing**: Every capability module SHOULD expose a `validate_*` function (already present in all 6 modules) as a lightweight architectural test hook, standardizing the pattern of provision → execute → validate → report seen across `automation.py`, `backup.py`, `dr_platform.py`.
3. **Impact-detection as CI gate**: `scripts/detect-impact.py` (`main`, `build_impacted_capabilities`, `build_doc_request`, `write_json`) becomes the authoritative mechanism that, on every PR, reads `read_changed_files`, resolves capabilities via `resolve_capabilities_for_changed_file` against `path_mapping`, and emits a JSON doc-request artifact via `write_json` used downstream for documentation regeneration.
4. **Path-mapping governance**: The `path_mapping` configuration consumed by `resolve_product` and `resolve_capabilities_for_changed_file` must be treated as a first-class architecture artifact, version-controlled and reviewed whenever new modules are added.
5. **Deferred OO refactor**: Class-based abstraction (Option C) is not adopted now but is recorded as a future revisit trigger once cross-hyperscaler (`vmc`) polymorphism or stateful workflow orchestration requirements emerge.

## Decision Outcome

**Decision: Adopt Option D** — Retain and formalize the domain-aligned, function-based modular architecture (one module per capability domain) and elevate `scripts/detect-impact.py` from an ad-hoc utility into a mandatory, pipeline-enforced capability-impact traceability gate.

Rationale:
- The existing repository structure already demonstrates strong, unforced alignment between files and product capabilities (evidenced by per-file `detected_domains` matching the product capability catalog almost exactly), indicating this is the platform's natural evolutionary direction rather than an imposed pattern.
- The presence of a purpose-built impact-detection script indicates the working group has already anticipated the traceability problem; formalizing rather than replacing it minimizes rework.
- A monolithic module (Option B) would degrade the current clean domain/capability alignment and increase merge-conflict risk across teams owning different capabilities (e.g., security vs. DR).
- Full OO refactor (Option C) is premature given zero classes exist today, minimal import surface (4 imports total), and no evidence of shared state requiring encapsulation; it is deferred, not rejected.

## Related Artifacts (Optional)

- `scripts/detect-impact.py` — capability-impact detection and documentation-request generation pipeline.
- `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py` — domain capability modules under governance.
- Product Capability Catalog (`compute`, `storage`, `networking`, `automation`, `monitoring`, `security`, `disaster-recovery`, `backup`, `containers`, `multi-tenancy`, `lifecycle-management`, `public-cloud-integration`, `reporting`, `api-service-broker`).
- Product Technology Catalog (vSphere/ESXi/vCenter/vSAN/NSX-T, Aria Suite, Tanzu Kubernetes Grid/Tanzu Mission Control, SDDC Manager, vLCM, Trend Micro, Nessus, HashiCorp Vault, Canopy/Avamar/Data Domain, SRM/vSphere Replication/HCX, VMC, Service Broker).
- Future ADR (to be raised): "Class-Based Refactor of Capability Modules for Multi-Hyperscaler Extensibility" — triggered by Option C deferral.

## Comments (Optional)

- Reviewers should confirm whether `path_mapping` in `scripts/detect-impact.py` currently enumerates all 8 scanned files, or whether gaps exist that would cause `resolve_capabilities_for_changed_file` to return an empty capability list for a changed file.
- The regex-fallback parse status on `scripts/detect-impact.py`, `src/backup.py`, and `src/dr_platform.py` should be investigated by the working group as a secondary code-health action item, independent of this ADR's decision.
- No test files were detected in the 8 scanned files; a follow-up ADR or backlog item may be warranted to formalize automated testing strategy alongside the `validate_*` function convention.
