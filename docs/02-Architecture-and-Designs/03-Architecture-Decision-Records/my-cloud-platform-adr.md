# Architecture Decision Record: Capability-Aligned Automation Architecture with Unified API Service Broker for my-cloud-platform

* **Status:** Proposed
* **Owner:** Enterprise Architecture Team
* **Deciders:** Cloud Platform Architect, Automation Engineering Lead, Security Architect, DR/BCP Lead, Platform Product Owner
* **Working Group:** Platform Engineering (Automation, Security, Backup/DR, Service Broker squads)
* **Creation Date:** 2024-02-08
* **Last Revisited:** 2024-02-08
* **Revision:** 1.0

---

## Context and Problem Statement

`my-cloud-platform` is the automation and orchestration codebase underpinning a VMware Cloud Foundation-based private/hybrid cloud offering (VCS-style architecture) spanning compute (vSphere/ESXi), storage (vSAN), networking (NSX-T), automation (Aria Automation/Orchestrator), monitoring (Aria Operations/Logs), security (Vault, Trend Micro, Nessus), disaster recovery (SRM, vSphere Replication), backup (Canopy, Avamar, Data Domain), containers (Tanzu Kubernetes Grid), multi-tenancy, lifecycle management (SDDC Manager, vLCM), public cloud integration (VMC, HCX), reporting, and an API/service broker consumption layer.

Repository analysis of `jijeeshlearningorg/greenfield-code` shows an early-stage, **flat, function-based Python codebase** (no classes detected, 41 functions across 8 files) with modules directly mapped to platform capability domains:

- `src/automation.py` — provisioning and workflow orchestration (automation)
- `src/backup.py` — backup scheduling/execution/validation/reporting (backup)
- `src/deploy.py` — network, Kubernetes, AI, and data platform deployment plus observability validation (networking, containers, monitoring)
- `src/dr_platform.py` — recovery plans, failover, recovery objective validation (disaster-recovery)
- `src/security_vault.py` — vault namespaces, customer-managed keys, key rotation, policy validation (security)
- `src/service_broker.py` — service catalog publishing, API registration, subscriptions (api-service-broker)
- `scripts/detect-impact.py` — CI/CD tooling that maps changed files to impacted capabilities for automated documentation generation

There is currently **no formal service boundary, shared domain model, or interface contract** between these modules. Each capability is implemented as a loosely coupled set of standalone functions, invoked presumably by pipelines or higher-level orchestrators (e.g., Aria Automation workflows). As the platform scales toward multi-tenant, hybrid, API-driven consumption (per the `api-service-broker` capability and `service-broker` technology), an explicit architectural decision is required on:

1. How capability modules should be structured, versioned, and exposed.
2. Whether the `service_broker` module becomes the sole consumption gateway for all platform capabilities.
3. How cross-cutting concerns (security/Vault, DR, backup, monitoring) integrate consistently across capability modules.
4. How the existing CI/CD capability-impact detection mechanism should govern change management and documentation.

This decision must be made now because the platform is greenfield — establishing these patterns early avoids costly re-architecture as capability modules multiply and tenant/API consumption grows.

---

## Assumptions

- The platform continues to be built on VMware Cloud Foundation components (vSphere, vSAN, NSX-T, SDDC Manager) as the underlying infrastructure substrate; this ADR governs the automation/orchestration layer, not the infrastructure substrate itself.
- Aria Automation/Orchestrator remains the primary workflow execution engine invoking these Python modules; the modules are automation "adapters," not full application services.
- `hashicorp-vault` is the enterprise standard for secrets/key management and will not be replaced by a native VMware alternative.
- Backup and DR platforms (Canopy, Avamar, Data Domain, SRM, vSphere Replication) remain third-party integrations invoked via API/CLI wrappers rather than reimplemented.
- The `api-service-broker` capability is intended to become the single external-facing interface for tenants and consuming applications, per the `service-broker` technology entry.
- CI/CD pipelines will continue to rely on `scripts/detect-impact.py` (or its successor) to auto-detect impacted capabilities per change, feeding automated documentation and governance tooling.
- Multi-tenancy is a first-class, non-negotiable requirement, implying isolation boundaries must be enforceable at the module/API level, not bolted on later.
- Team is comfortable evolving from function-based scripts to a lightweight service/domain-oriented structure without introducing heavyweight microservice infrastructure prematurely.

---

## Constraints

- Must remain compatible with existing VMware Aria Suite Lifecycle-managed automation runtime (Aria Automation/Orchestrator invocation model).
- No breaking changes to already-integrated third-party platforms (Trend Micro, Nessus, Avamar, Data Domain, SRM, HCX, VMC) — integration must occur through their supported APIs/CLIs.
- Security and compliance mandates require all secrets/keys to flow through HashiCorp Vault (`security_vault.py`) — no direct credential handling in other modules.
- The repository currently has **no classes and no formal interface/module boundaries** — any architectural pattern chosen must be incrementally adoptable without a full rewrite.
- CI/CD capability-impact detection (`detect-impact.py`) is coupled to file path conventions (`path_mapping`); any restructuring of module locations must preserve or update this mapping to avoid breaking automated documentation.
- Multi-tenant isolation requirements constrain how shared modules (e.g., `security_vault.py`, `dr_platform.py`) manage per-tenant namespaces/state.
- Public cloud integration (VMC, HCX) requires the architecture to tolerate hybrid execution contexts (on-prem SDDC + hyperscaler).

---

## Considered Options

**Option A — Status Quo: Flat Script Collection**
Continue as a loose collection of standalone functions per file, invoked ad hoc by automation pipelines, with no defined module boundaries or consumption gateway.

**Option B — Capability-Aligned Modular Architecture with Unified Service Broker Gateway (Recommended)**
Formalize each existing module (`automation`, `backup`, `deploy`, `dr_platform`, `security_vault`, `service_broker`) as a bounded capability module with a defined function-level contract (inputs/outputs, error handling, idempotency). Expose all capabilities externally only through `service_broker.py` (API/catalog layer), which internally calls the other capability modules. Cross-cutting concerns (secrets, logging, tenancy context) are injected consistently across modules.

**Option C — Full Microservices Decomposition**
Break each capability into an independently deployable microservice with its own API, data store, and CI/CD pipeline, communicating via REST/gRPC and an API gateway.

**Option D — Monolithic Orchestration Framework**
Consolidate all capability logic into a single orchestration engine/class hierarchy (e.g., a `PlatformOrchestrator` object model) replacing the current flat-function style with an object-oriented framework.

---

## Proposed Design

Adopt **Option B: Capability-Aligned Modular Architecture with a Unified API Service Broker Gateway.**

1. **Capability Module Boundaries**
   Retain the current file-per-capability structure (`automation.py`, `backup.py`, `deploy.py`, `dr_platform.py`, `security_vault.py`, `service_broker.py`) but formalize each as a bounded module with:
   - A documented function contract (typed inputs/outputs, consistent boolean/dict return conventions already largely in place).
   - Idempotent, retry-safe operations suitable for orchestration by Aria Orchestrator workflows.
   - No cross-module direct calls except through `service_broker.py`.

2. **Unified Consumption Layer**
   `service_broker.py` (`publish_service_catalog`, `register_platform_api`, `create_service_offering`, `validate_api_subscription`) becomes the **sole external interface**. All tenant/application consumption of compute, storage, networking, backup, DR, and security capabilities is exposed as catalog offerings/APIs registered through this module — no direct external invocation of `automation.py`, `backup.py`, `dr_platform.py`, or `security_vault.py`.

3. **Cross-Cutting Security Integration**
   All modules requiring secrets/keys (backup credentials, DR site credentials, automation service accounts) obtain them exclusively via `security_vault.py` (`create_vault_namespace`, `assign_key_to_service`), enforcing a single secrets-management path and supporting per-tenant Vault namespaces for multi-tenancy isolation.

4. **Observability and Governance Hooks**
   `deploy.py`'s `validate_platform_observability` pattern is extended as a required post-deployment gate for all capability modules (automation, backup, DR), ensuring monitoring/logging integration (Aria Operations/Logs) is validated before a capability is marked "ready" in the service catalog.

5. **CI/CD Capability Impact Governance**
   Retain and extend `scripts/detect-impact.py` as the authoritative mechanism mapping code changes to impacted capabilities, driving automated ADR/documentation generation and change approval routing (e.g., changes to `security_vault.py` trigger mandatory security architecture review).

6. **Incremental Evolution Path**
   No introduction of classes/OOP frameworks or independent microservices at this stage; the function-based style is preserved but constrained by module contracts, enabling future evolution to Option C if scale demands it.

---

## Decision Outcome

**Decision:** Adopt the **Capability-Aligned Modular Architecture with a Unified API Service Broker Gateway (Option B)**.

Rationale:
- Preserves the existing lightweight, function-based codebase (minimal rework), while introducing the architectural discipline (module boundaries, single consumption gateway, mandatory Vault integration) needed for a multi-tenant, API-driven platform.
- Aligns directly with the `api-service-broker` capability and `service-broker` technology already identified in product metadata as the intended consumption model.
- Avoids premature complexity of full microservices (Option C) given current repository maturity (8 files, 41 functions, no classes), while avoiding the technical debt and governance risk of continuing an unstructured script collection (Option A).
- Rejects Option D (monolithic OO framework) as unnecessary overhead at this stage and misaligned with the existing procedural style already adopted by the engineering team.
- Leverages the existing `detect-impact.py` capability-mapping mechanism as a built-in governance control, requiring only path-mapping maintenance rather than new tooling.

---

## Related Artifacts

- `src/automation.py`, `src/backup.py`, `src/deploy.py`, `src/dr_platform.py`, `src/security_vault.py`, `src/service_broker.py`
- `scripts/detect-impact.py` (capability-impact detection and doc-generation pipeline)
- Product capability catalog: `compute`, `storage`, `networking`, `automation`, `monitoring`, `security`, `disaster-recovery`, `backup`, `containers`, `multi-tenancy`, `lifecycle-management`, `public-cloud-integration`, `reporting`, `api-service-broker`
- VMware Cloud Foundation reference architecture (vSphere, vSAN, NSX-T, SDDC Manager, vLCM)
- Aria Suite Lifecycle Manager design documentation
- Enterprise Secrets Management Standard (HashiCorp Vault)
- Future ADR (tracked): Microservices decomposition evaluation criteria and triggers (successor to Option C)

---

## Comments

*To be populated during ADR review cycle. Reviewers should record concerns regarding: (1) tenant isolation enforcement within `security_vault.py` namespaces, (2) SLA/idempotency guarantees required in `dr_platform.py` failover functions, (3) versioning strategy for APIs registered via `service_broker.py`, and (4) maintenance ownership of the `detect-impact.py` path-mapping configuration.*
