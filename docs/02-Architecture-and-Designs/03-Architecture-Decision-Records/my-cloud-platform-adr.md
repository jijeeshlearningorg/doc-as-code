# 02. Architecture Decision Record (ADR) template

* **Status:** Draft  
* **Owner:** Enterprise Architecture Team  
* **Deciders:** Lead Architect, Cloud Ops Lead  
* **Working group:** Cloud Platform Deployment Team  
* **Creation Date:** 2024-02-08  
* **Last Revisited:** 2024-02-08  
* **Revision:** 1  

## Context and Problem Statement

The My Cloud Services platform is undergoing a greenfield deployment of its network foundation. The current repository `jijeeshlearningorg/greenfield-code` contains a new deployment script `src/deploy.py` that introduces network provisioning logic. The architectural design issue is how to integrate VMware NSX‑T with network visibility and monitoring capabilities while maintaining automation and observability across the platform. This decision is required now to align the deployment pipeline with the product’s networking and observability capabilities and to ensure that future platform expansions can leverage automated, monitored network infrastructure.

## Assumptions (Optional)

* The platform will run on VMware vSphere infrastructure with NSX‑T as the primary SDN solution.  
* Aria Automation and Aria Network Insight are available and licensed.  
* CI/CD will continue to use GitHub Actions.  
* The deployment will be executed in a greenfield environment with no legacy network constraints.

## Constraints (Optional)

* Licensing limits for Aria Network Insight may restrict the number of monitored segments.  
* The deployment must not disrupt existing AI, data, or Kubernetes workloads.  
* The solution must be compatible with the existing `deploy_ai_platform`, `deploy_data_platform`, and `deploy_kubernetes_platform` functions.

## Considered Options

| Option | Description | Pros | Cons |
|--------|-------------|------|------|
| **1. NSX‑T + Aria Network Insight via Aria Automation** | Automate NSX‑T deployment and integrate network visibility through Aria Network Insight using Aria Automation workflows. | • Full automation, minimal manual steps.<br>• Built‑in observability for network traffic.<br>• Consistent with existing Aria Automation usage. | • Requires Aria Network Insight licensing.<br>• Additional learning curve for automation scripts. |
| **2. NSX‑T standalone with manual configuration** | Deploy NSX‑T using standard vSphere tools and manually configure network segments. | • No extra licensing.<br>• Simpler initial setup. | • High operational overhead.<br>• No automated observability. |
| **3. VMware vSphere networking (no NSX‑T)** | Use vSphere standard networking instead of NSX‑T. | • Lower complexity.<br>• No additional licensing. | • Lacks advanced SDN features.<br>• Limited observability. |
| **4. External SDN solution (e.g., OpenDaylight)** | Replace NSX‑T with an open‑source SDN controller. | • Potential cost savings.<br>• Flexibility. | • Requires significant integration effort.<br>• Not aligned with existing VMware stack. |

## Proposed Design (Optional)

Adopt **Option 1**: Deploy NSX‑T with Aria Network Insight integrated via Aria Automation. The `src/deploy.py` script will invoke Aria Automation workflows that provision NSX‑T components, configure logical switches, routers, and security policies, and register the network with Aria Network Insight for continuous monitoring. The CI/CD pipeline (GitHub Actions) will trigger this script during the deployment phase.

## Decision Outcome

The architecture will **automatically provision NSX‑T and integrate Aria Network Insight** using Aria Automation. This approach aligns with the product’s networking and observability capabilities, reduces manual effort, and ensures consistent network visibility across the platform.

## Related Artifacts (Optional)

* ADR-001: Network Foundation Deployment Strategy  
* ADR-002: Observability Integration for Network Traffic  
* High‑Level Design: `template/docs/docs/01. Architecture & Designs/01. High Level Designs/index.md`  
* Low‑Level Design: `template/docs/docs/01. Architecture & Designs/02. Low Level Designs/index.md`

## Comments (Optional)

* **Lead Architect:** “Automation is critical for scaling the platform. This decision will reduce deployment time by ~70%.”  
* **Cloud Ops Lead:** “We need to ensure Aria Network Insight licensing is secured before finalizing the rollout.”  
* **DevOps Engineer:** “GitHub Actions workflow will need to be updated to include the new deployment script.”  

---  

**Impacted Capabilities**  
- **Networking** – Automated NSX‑T provisioning and configuration.  
- **Observability** – Continuous network traffic monitoring via Aria Network Insight.  

**Source Repository Changes**  
- `src/deploy.py` – Updated to invoke Aria Automation workflows for NSX‑T and Aria Network Insight.  

**Architectural Consequences**  
- Increased reliance on Aria Automation and Aria Network Insight.  
- Additional licensing and operational overhead for network monitoring.  
- CI/CD pipeline modifications to support automated deployment.  
- Enhanced observability across the platform, enabling proactive network issue detection.
