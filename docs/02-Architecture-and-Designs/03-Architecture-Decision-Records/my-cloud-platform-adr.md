# 02. Architecture Decision Record (ADR) template

* Status: Proposed  
* Owner: architecture-team  
* Deciders: Architecture Review Board  
* Working group: Platform Engineering Team  
* Creation Date: 2024-09-26  
* Last Revisited: 2024-09-26  
* Revision: 1  

## Context and Problem Statement

The My Cloud Services product (key: my-cloud-platform) is being extended with a new networking foundation that must be deployed consistently across the ai‑platform, data‑platform, and kubernetes environments. Recent changes in the source repository (`src/deploy.py`) introduce automated deployment logic for network services, but there is no agreed‑upon approach for integrating NSX‑T based virtual networking with aria‑network‑insight observability while ensuring the impacted capabilities (ai‑platform, data‑platform, kubernetes, networking, observability) remain functional.

The decision must define how the networking foundation will be provisioned, monitored, and integrated into the existing CI/CD pipeline, addressing the need for repeatable, observable, and supportable network configuration in the greenfield codebase.

## Assumptions (Optional)

- The environment already includes a VMware vSphere foundation with NSX‑T installed and licensed.  
- aria‑network‑insight is available for network telemetry and analytics.  
- The CI/CD pipeline uses GitHub Actions for automation.  
- No additional networking technologies beyond those listed in the technology catalog may be introduced.  
- The deployment scripts must be idempotent and compatible with existing `deploy_*` functions.

## Constraints (Optional)

- Must leverage only technologies present in the `technology_catalog` (e.g., nsx‑t, aria‑network‑insight).  
- Changes to `src/deploy.py` must not break existing deployments of ai‑platform, data‑platform, or kubernetes.  
- The solution must provide observability hooks for the networking layer to support future troubleshooting.  
- Implementation timeline is limited to the current sprint (2 weeks).

## Considered Options

| Option | Description | Concerns / Trade‑offs |
|--------|-------------|-----------------------|
| **1. Deploy NSX‑T via Ansible playbooks** | Use existing Ansible inventory to provision NSX‑T segments, then integrate aria‑network‑insight for monitoring. | Requires maintenance of Ansible scripts; may increase deployment time. |
| **2. Use VMware HCX for network abstraction** | Leverage HCX to manage network services as a higher‑level abstraction. | Introduces an additional VMware product not currently in the catalog; may add licensing overhead. |
| **3. Stick with manual NSX‑T configuration** | Continue manual network setup performed by operations staff. | Not repeatable, conflicts with automation goals, increases risk of drift. |

The team evaluated each option against the impacted capabilities (ai‑platform, data‑platform, kubernetes, networking, observability) and the constraints listed above. Option 1 aligns best with the need for automated, observable, and supportable networking while staying within the approved technology set.

## Proposed Design (Optional)

Adopt NSX‑T as the underlying virtual networking substrate, extend the existing deployment pipeline to include:

1. **Network Foundation Deployment** – Add a new function `deploy_network_foundation` that configures NSX‑T segments, edge services, and security policies using Ansible.  
2. **Observability Integration** – Configure aria‑network‑insight to collect flow and performance metrics, exposing them to the `observability` capability.  
3. **Update Deployment Scripts** – Modify `src/deploy.py` to call `deploy_network_foundation` before `deploy_ai_platform`, `deploy_data_platform`, and `deploy_kubernetes_platform`.  
4. **Validation** – Add a validation step `validate_platform_observability` to verify that network telemetry is correctly reported.

## Decision Outcome

**Accepted:** The Architecture will use NSX‑T based virtual networking integrated with aria‑network‑insight for observability. The deployment scripts will be updated to include a dedicated `deploy_network_foundation` step, and the existing `src/deploy.py` file will be modified accordingly. This choice satisfies the impacted capabilities (ai‑platform, data‑platform, kubernetes, networking, observability) and adheres to the defined constraints.

## Related Artifacts (Optional)

- High Level Design: `template/docs/docs/01. Architecture & Designs/01. High Level Designs/index.md`  
- Low Level Design: `template/docs/docs/01. Architecture & Designs/02. Low Level Designs/index.md`  
- Build & Installation Specifications: `template/docs/docs/02. Build & Installation specifications/index.md`  
- Operations & Support: `template/docs/docs/03. Operations & Support/index.md`  
- Knowledge Base: `template/docs/docs/04. Knowledge Base/index.md`  

## Comments (Optional)

*Comment from Network Engineering Lead:* "We need to ensure that the NSX‑T segment IDs are documented in the service catalog to avoid conflicts with existing workloads."  
*Comment from Security Team:* "Review the security policies applied via NSX‑T to confirm compliance with internal hardening standards."  

---  

*All author guidance blocks have been removed per instructions.*
