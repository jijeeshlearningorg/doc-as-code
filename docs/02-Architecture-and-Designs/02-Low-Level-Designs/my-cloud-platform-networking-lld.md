# Low-Level Design (LLD): My Cloud Platform

**Author:** Lead Solution Architect  
**Date:** 2024  
**Version:** 1.0  
**Status:** Final  
**Owner:** Platform Architecture Team  

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Solution Architect | Lead Solution Architect | Pending | TBD |
| Security Architect | Security Team | Pending | TBD |
| Platform Owner | Platform Owner | Pending | TBD |
| Service Owner | Service Owner | Pending | TBD |
| Operations Representative | Operations Team | Pending | TBD |

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Architecture Review Board | ARB | TBD | Initial Review |

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | 2024 | Initial LLD Document | Lead Solution Architect |

---

# 2. Related Documents

| Document Type | Document Reference | Link | Relationship |
|----------|----------|----------|----------|
| HLD | My Cloud Platform HLD | [Reference] | Parent Design |
| LLD | This Document | [Current] | Current Document |
| BIG | Build & Installation Guide | [Reference] | Build Guide |
| OPG | Operations Guide | [Reference] | Operations Guide |
| ADR | Architecture Decision Records | [Reference] | Design Decisions |
| Repository | greenfield-code | https://github.com/jijeeshlearningorg/greenfield-code | Source Code |
| Vendor Documentation | VMware vSphere | [Reference] | Reference |
| Vendor Documentation | VMware NSX-T | [Reference] | Reference |
| Vendor Documentation | VMware vSAN | [Reference] | Reference |
| Vendor Documentation | VMware Aria Suite | [Reference] | Reference |
| Vendor Documentation | Tanzu Kubernetes Grid | [Reference] | Reference |

---

# 3. HLD Traceability Matrix

| HLD Requirement | HLD Section | LLD Section | Implementation Approach |
|----------|----------|----------|----------|
| Compute Platform Provisioning | Compute Architecture | 7.1, 13 | Automated provisioning via Aria Automation with vSphere integration |
| Storage Services | Storage Architecture | 7.2, 13 | vSAN software-defined storage with optional Fibre Channel integration |
| Network Virtualization | Network Architecture | 7.3, 13 | NSX-T overlay networking with segmentation and routing |
| Kubernetes Platform | Container Architecture | 7.1, 13 | Tanzu Kubernetes Grid deployment with TMC governance |
| Monitoring & Observability | Monitoring Architecture | 14.1, 14.2 | Aria Operations, Aria Logs, and Aria Network Insight integration |
| Security & Compliance | Security Architecture | 10, 13 | HashiCorp Vault, Trend Micro, Nessus vulnerability scanning |
| Disaster Recovery | DR Architecture | 11.2, 13 | SRM and vSphere Replication with automated failover |
| Backup Services | Backup Architecture | 11.3, 13 | Canopy Enterprise Backup with Avamar and Data Domain |
| API Service Broker | Service Delivery | 9, 13 | Service Broker portal with API registration and catalog management |
| Lifecycle Management | Platform Lifecycle | 16, 13 | SDDC Manager, vLCM, and Aria Suite Lifecycle Manager |
| Multi-Tenancy | Tenant Isolation | 10.1, 10.2 | RBAC-based tenant separation with namespace isolation |
| Public Cloud Integration | Cloud Integration | 9.1, 13 | VMware Cloud and HCX for workload mobility |

---

# 4. Design Inputs

## 4.1 Design References

- VMware Cloud Foundation Architecture
- VMware vSphere 8.0 Documentation
- VMware NSX-T 4.x Documentation
- VMware Aria Suite Documentation
- Tanzu Kubernetes Grid Documentation
- HashiCorp Vault Enterprise Documentation
- Kubernetes Best Practices
- Cloud Native Security Standards
- NIST Cybersecurity Framework
- ISO 27001 Security Standards

## 4.2 Technical Constraints

- vSphere 8.0 minimum version requirement
- NSX-T 4.x for network virtualization
- Kubernetes 1.27+ compatibility
- HashiCorp Vault for secrets management
- Multi-site disaster recovery capability
- Compliance with enterprise security policies
- Integration with existing monitoring infrastructure
- Support for heterogeneous storage backends
- Multi-tenancy with logical isolation
- API-first service delivery model

## 4.3 Design Drivers

- High availability (99.99% uptime target)
- Automated provisioning and lifecycle management
- Security-first architecture with encryption everywhere
- Multi-tenancy with complete tenant isolation
- Kubernetes-native workload support
- Disaster recovery with RTO < 4 hours, RPO < 1 hour
- Comprehensive monitoring and observability
- Self-service service delivery through APIs
- Compliance automation and reporting
- Scalability to support 1000+ virtual machines

---

# 5. Implementation Decisions & Alternatives

| Decision | Alternatives Considered | Rationale |
|----------|----------|----------|
| Use Aria Automation for provisioning orchestration | Terraform, Ansible, custom scripts | Native VMware integration, multi-cloud support, built-in RBAC |
| Implement NSX-T for network virtualization | OVN, Calico, Flannel | Enterprise-grade security, micro-segmentation, advanced routing |
| Deploy vSAN for software-defined storage | Pure Storage, NetApp, Ceph | Integrated with vSphere, hyperconverged architecture, cost-effective |
| Use HashiCorp Vault for secrets management | AWS Secrets Manager, Azure Key Vault, Kubernetes Secrets | Multi-cloud support, enterprise features, audit logging |
| Implement Tanzu Kubernetes Grid | EKS, AKS, GKE | VMware-native, consistent across on-premises and cloud |
| Deploy Aria Operations for monitoring | Prometheus, Datadog, New Relic | VMware-native, integrated with vSphere, advanced analytics |
| Use SRM for disaster recovery | Zerto, Nakivo, manual replication | Native VMware integration, proven enterprise solution |
| Implement Canopy Enterprise Backup | Veeam, Commvault, Acronis | Enterprise-grade, multi-platform support, deduplication |
| Deploy Service Broker for API delivery | Kong, AWS API Gateway, custom portal | Native VMware integration, self-service catalog, RBAC |
| Use SDDC Manager for lifecycle management | Ansible, Terraform, manual processes | Integrated platform management, automated patching, compliance |

---

# 6. Detailed Architecture

## 6.1 Logical Design

### 6.1.1 Component Interaction Model

```
┌─────────────────────────────────────────────────────────────────┐
│                    Service Broker Portal                         │
│              (API Gateway & Self-Service Catalog)                │
└────────────────────────┬────────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼────────┐ ┌────▼──────────┐ ┌──▼──────────────┐
│  Automation    │ │  Deployment   │ │  Lifecycle      │
│  (Aria Auto)   │ │  (Terraform)  │ │  (SDDC Manager) │
└────────┬───────┘ └────┬──────────┘ └──┬──────────────┘
         │               │               │
         └───────────────┼───────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼────────┐ ┌────▼──────────┐ ┌──▼──────────────┐
│  Compute       │ │  Storage      │ │  Network        │
│  (vSphere)     │ │  (vSAN)       │ │  (NSX-T)        │
└────────┬───────┘ └────┬──────────┘ └──┬──────────────┘
         │               │               │
         └───────────────┼───────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
┌───────▼────────┐ ┌────▼──────────┐ ┌──▼──────────────┐
│  Monitoring    │ │  Security     │ │  Backup/DR      │
│  (Aria Ops)    │ │  (Vault)      │ │  (SRM/Canopy)   │
└────────────────┘ └───────────────┘ └─────────────────┘
```

### 6.1.2 Service Communication Patterns

- **Synchronous**: REST APIs via Service Broker for immediate operations
- **Asynchronous**: Workflow orchestration via Aria Orchestrator for long-running tasks
- **Event-Driven**: Monitoring alerts triggering automated remediation
- **Batch**: Scheduled jobs for backup, patching, and reporting

### 6.1.3 Internal Dependencies

```
Automation Layer
├── Aria Automation (Provisioning Orchestration)
│   ├── Depends on: vSphere API, NSX-T API, vSAN API
│   ├── Depends on: Aria Orchestrator (Workflow Engine)
│   └── Depends on: HashiCorp Vault (Credentials)
├── Aria Orchestrator (Workflow Engine)
│   ├── Depends on: vCenter API
│   ├── Depends on: NSX-T API
│   └── Depends on: External REST APIs
└── SDDC Manager (Lifecycle Management)
    ├── Depends on: vCenter
    ├── Depends on: NSX-T Manager
    └── Depends on: vLCM

Infrastructure Layer
├── vSphere (Compute)
│   ├── Depends on: vCenter (Management)
│   ├── Depends on: NSX-T (Networking)
│   └── Depends on: vSAN (Storage)
├── NSX-T (Networking)
│   ├── Depends on: vSphere (Hypervisor)
│   └── Depends on: Aria Operations (Monitoring)
└── vSAN (Storage)
    ├── Depends on: vSphere (Hypervisor)
    └── Depends on: Aria Operations (Monitoring)

Kubernetes Layer
├── Tanzu Kubernetes Grid
│   ├── Depends on: vSphere (Infrastructure)
│   ├── Depends on: NSX-T (Networking)
│   └── Depends on: Tanzu Mission Control (Governance)
└── Tanzu Mission Control
    ├── Depends on: Kubernetes Clusters
    └── Depends on: Aria Operations (Monitoring)

Security Layer
├── HashiCorp Vault (Secrets Management)
│   ├── Depends on: Network Connectivity
│   └── Depends on: Persistent Storage
├── Trend Micro (Endpoint Protection)
│   ├── Depends on: vSphere (VM Deployment)
│   └── Depends on: Network Connectivity
└── Nessus (Vulnerability Scanning)
    ├── Depends on: Network Connectivity
    └── Depends on: Target Systems

Observability Layer
├── Aria Operations (Monitoring)
│   ├── Depends on: vSphere API
│   ├── Depends on: NSX-T API
│   └── Depends on: vSAN API
├── Aria Logs (Log Aggregation)
│   ├── Depends on: Log Sources
│   └── Depends on: Persistent Storage
└── Aria Network Insight (Network Analytics)
    ├── Depends on: NSX-T API
    └── Depends on: Network Traffic

Backup & DR Layer
├── SRM (Site Recovery Manager)
│   ├── Depends on: vCenter
│   ├── Depends on: vSphere Replication
│   └── Depends on: Network Connectivity
├── vSphere Replication
│   ├── Depends on: vSphere
│   └── Depends on: Network Connectivity
└── Canopy Enterprise Backup
    ├── Depends on: Avamar (Backup Engine)
    ├── Depends on: Data Domain (Storage)
    └── Depends on: Network Connectivity

Service Delivery Layer
└── Service Broker
    ├── Depends on: Aria Automation
    ├── Depends on: API Registry
    └── Depends on: Catalog Database
```

## 6.2 Physical Design

### 6.2.1 On-Premises Deployment

#### Datacenter Layout

```
┌─────────────────────────────────────────────────────────────┐
│                    Primary Datacenter                        │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Management Cluster (3 Nodes)                 │   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │   │
│  │  │  vCenter    │  │  NSX-T Mgr  │  │  SDDC Mgr   │  │   │
│  │  │  Aria Ops   │  │  Aria Auto  │  │  Vault      │  │   │
│  │  └─────────────┘  └─────────────┘  └─────────────┘  │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │      Compute Cluster (6-12 Nodes)                    │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐           │   │
│  │  │ ESXi 1   │  │ ESXi 2   │  │ ESXi N   │  ...      │   │
│  │  │ vSAN     │  │ vSAN     │  │ vSAN     │           │   │
│  │  └──────────┘  └──────────┘  └──────────┘           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │    Kubernetes Cluster (3-6 Nodes)                    │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐           │   │
│  │  │ TKG Node │  │ TKG Node │  │ TKG Node │  ...      │   │
│  │  └──────────┘  └──────────┘  └──────────┘           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │    Edge Cluster (2-3 Nodes)                          │   │
│  │  ┌──────────┐  ┌──────────┐                          │   │
│  │  │ NSX Edge │  │ NSX Edge │                          │   │
│  │  └──────────┘  └──────────┘                          │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                  Secondary Datacenter (DR)                   │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │    Management Cluster (3 Nodes - Standby)           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │    Compute Cluster (6-12 Nodes - Standby)           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │    Backup Infrastructure                             │   │
│  │  ┌──────────────┐  ┌──────────────┐                 │   │
│  │  │ Avamar       │  │ Data Domain  │                 │   │
│  │  │ (Backup Eng) │  │ (Backup Stor)│                 │   │
│  │  └──────────────┘  └──────────────┘                 │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

#### Cluster Configuration

| Cluster Type | Node Count | CPU per Node | Memory per Node | Storage | Purpose |
|----------|----------|----------|----------|----------|----------|
| Management | 3 | 16 cores | 128 GB | 500 GB SSD | vCenter, NSX-T, Aria, SDDC Manager |
| Compute | 6-12 | 32 cores | 256 GB | 2 TB SSD + 10 TB HDD | VM Workloads, vSAN |
| Kubernetes | 3-6 | 16 cores | 64 GB | 500 GB SSD | TKG Control Plane & Workers |
| Edge | 2-3 | 8 cores | 32 GB | 200 GB SSD | NSX-T Edge Services |
| DR Standby | 6-12 | 32 cores | 256 GB | 2 TB SSD + 10 TB HDD | Disaster Recovery Standby |

### 6.2.2 Network Topology

#### Physical Network

```
┌─────────────────────────────────────────────────────────────┐
│                    Core Network                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Core Switches (Redundant Pair)                      │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │ VLAN 100: Management                            │ │   │
│  │  │ VLAN 101: vMotion                               │ │   │
│  │  │ VLAN 102: vSAN                                  │ │   │
│  │  │ VLAN 103: NSX-T Overlay                         │ │   │
│  │  │ VLAN 104: Kubernetes                            │ │   │
│  │  │ VLAN 105: Backup                                │ │   │
│  │  │ VLAN 106: Disaster Recovery                     │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Distribution Switches (Per Cluster)                │   │
│  │  ├─ Management Cluster Switch                       │   │
│  │  ├─ Compute Cluster Switch                          │   │
│  │  ├─ Kubernetes Cluster Switch                       │   │
│  │  └─ Edge Cluster Switch                             │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Access Switches (Per Rack)                         │   │
│  │  ├─ Rack 1 Switch                                   │   │
│  │  ├─ Rack 2 Switch                                   │   │
│  │  └─ Rack N Switch                                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

#### Logical Network (NSX-T Overlay)

```
┌─────────────────────────────────────────────────────────────┐
│                  NSX-T Overlay Network                       │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Tier-0 Gateway (Active-Active)                      │   │
│  │  ├─ North-South Routing                             │   │
│  │  ├─ External Connectivity                           │   │
│  │  └─ BGP Peering                                     │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Tier-1 Gateways (Per Tenant)                        │   │
│  │  ├─ Tenant-A Gateway                                │   │
│  │  ├─ Tenant-B Gateway                                │   │
│  │  └─ Tenant-N Gateway                                │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Logical Switches (Per Segment)                      │   │
│  │  ├─ Management Segment (10.0.1.0/24)                │   │
│  │  ├─ Compute Segment (10.0.2.0/24)                   │   │
│  │  ├─ Kubernetes Segment (10.0.3.0/24)                │   │
│  │  ├─ Tenant-A Segment (10.1.0.0/24)                  │   │
│  │  ├─ Tenant-B Segment (10.2.0.0/24)                  │   │
│  │  └─ Tenant-N Segment (10.N.0.0/24)                  │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Distributed Firewall Rules                          │   │
│  │  ├─ Tenant Isolation Rules                          │   │
│  │  ├─ Application Security Rules                      │   │
│  │  ├─ Compliance Rules                                │   │
│  │  └─ Threat Prevention Rules                         │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 6.2.3 Kubernetes Cluster Architecture

```
┌─────────────────────────────────────────────────────────────┐
│              Tanzu Kubernetes Grid Cluster                   │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Control Plane (3 Nodes - HA)                        │   │
│  │  ├─ API Server                                       │   │
│  │  ├─ etcd (Distributed)                              │   │
│  │  ├─ Controller Manager                              │   │
│  │  └─ Scheduler                                        │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Worker Node Pools                                   │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │ General Purpose Pool (3-6 Nodes)                │ │   │
│  │  │ ├─ Kubelet                                      │ │   │
│  │  │ ├─ Container Runtime (containerd)               │ │   │
│  │  │ └─ CNI (NSX-T)                                  │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │ Compute-Intensive Pool (2-4 Nodes)              │ │   │
│  │  │ ├─ High CPU/Memory                              │ │   │
│  │  │ └─ Taints for Workload Isolation                │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  │  ┌─────────────────────────────────────────────────┐ │   │
│  │  │ Storage-Intensive Pool (2-4 Nodes)              │ │   │
│  │  │ ├─ High Storage Capacity                        │ │   │
│  │  │ └─ Persistent Volume Support                    │ │   │
│  │  └─────────────────────────────────────────────────┘ │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Namespace Structure                                 │   │
│  │  ├─ kube-system (System Components)                 │   │
│  │  ├─ kube-public (Public Resources)                  │   │
│  │  ├─ kube-node-lease (Node Heartbeats)               │   │
│  │  ├─ monitoring (Prometheus, Grafana)                │   │
│  │  ├─ logging (Fluent Bit, Loki)                      │   │
│  │  ├─ security (Vault Agent, Cert Manager)            │   │
│  │  ├─ tenant-a (Tenant A Workloads)                   │   │
│  │  ├─ tenant-b (Tenant B Workloads)                   │   │
│  │  └─ tenant-n (Tenant N Workloads)                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Add-on Components                                   │   │
│  │  ├─ CNI: NSX-T Container Plugin                     │   │
│  │  ├─ Ingress: NSX-T Ingress Controller               │   │
│  │  ├─ Storage: vSAN CSI Driver                        │   │
│  │  ├─ Monitoring: Prometheus Operator                 │   │
│  │  ├─ Logging: Fluent Bit + Loki                      │   │
│  │  ├─ Security: Vault Agent Injector                  │   │
│  │  ├─ Cert Management: cert-manager                   │   │
│  │  └─ Service Mesh: Istio (Optional)                  │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

---

# 7. Component Design

## 7.1 Compute / Runtime Design

### 7.1.1 vSphere Compute Platform

#### Virtual Machine Provisioning

**Function**: `provision_infrastructure(environment_name: str) -> bool`

```python
# Implementation approach
def provision_infrastructure(environment_name):
    """
    Automates infrastructure provisioning using Aria Automation
    
    Process:
    1. Validate environment configuration
    2. Reserve compute resources from vSphere cluster
    3. Allocate network segments via NSX-T
    4. Provision storage from vSAN
    5. Deploy VMs with baseline configuration
    6. Apply security policies
    7. Validate deployment
    """
    # Connects to vCenter API
    # Retrieves resource pools for environment
    # Creates VM from template
    # Configures vNICs with NSX-T segments
    # Applies resource limits (CPU, Memory, Storage)
    # Returns deployment status
```

**VM Sizing Strategy**:

| VM Type | CPU | Memory | Storage | Use Case |
|----------|----------|----------|----------|----------|
| Micro | 1-2 | 2-4 GB | 20 GB | Development, Testing |
| Small | 2-4 | 8-16 GB | 50 GB | Small Applications |
| Medium | 4-8 | 16-32 GB | 100 GB | Standard Workloads |
| Large | 8-16 | 32-64 GB | 200 GB | Enterprise Applications |
| XLarge | 16+ | 64+ GB | 500+ GB | High-Performance Computing |

**VM Deployment Process**:

1. **Template Selection**: Choose from pre-configured VM templates
2. **Customization**: Apply environment-specific configuration
3. **Resource Allocation**: Assign CPU, memory, and storage
4. **Network Configuration**: Connect to NSX-T segments
5. **Security Hardening**: Apply baseline security policies
6. **Monitoring Setup**: Register with Aria Operations
7. **Backup Configuration**: Enable backup policies

#### vSphere Cluster Configuration

**Management Cluster**:
- 3 nodes for high availability
- Dedicated to management components
- Resource reservation: 50% CPU, 50% Memory
- DRS enabled with aggressive settings
- HA enabled with admission control

**Compute Cluster**:
- 6-12 nodes for workload hosting
- Resource reservation: 20% CPU, 30% Memory
- DRS enabled with balanced settings
- HA enabled with admission control
- vSAN enabled for distributed storage

**Kubernetes Cluster**:
- 3-6 nodes for Kubernetes infrastructure
- Resource reservation: 30% CPU, 40% Memory
- DRS enabled with conservative settings
- HA enabled with admission control

#### Resource Pools

```
Root
├── Management (50% CPU, 50% Memory)
│   ├── vCenter (Reserved)
│   ├── NSX-T (Reserved)
│   ├── Aria (Reserved)
│   └── SDDC Manager (Reserved)
├── Compute (40% CPU, 40% Memory)
│   ├── Tenant-A (20% CPU, 20% Memory)
│   ├── Tenant-B (20% CPU, 20% Memory)
│   └── Tenant-N (20% CPU, 20% Memory)
└── Kubernetes (10% CPU, 10% Memory)
    ├── Control Plane (Reserved)
    ├── Worker Nodes (Shared)
    └── Add-ons (Reserved)
```

### 7.1.2 Tanzu Kubernetes Grid Deployment

**Function**: `deploy_kubernetes_platform(cluster_name: str) -> bool`

```python
def deploy_kubernetes_platform(cluster_name):
    """
    Deploys Kubernetes platform services for cloud workloads
    
    Process:
    1. Validate cluster prerequisites
    2. Deploy TKG management cluster
    3. Deploy TKG workload clusters
    4. Configure CNI (NSX-T)
    5. Deploy storage drivers (vSAN CSI)
    6. Configure ingress (NSX-T Ingress)
    7. Deploy monitoring stack
    8. Deploy logging stack
    9. Configure security policies
    10. Register with TMC
    """
    # Validates vSphere resources
    # Creates VM templates for TKG
    # Deploys Kubernetes control plane
    # Joins worker nodes
    # Configures networking
    # Deploys add-ons
    # Returns cluster status
```

**TKG Cluster Architecture**:

| Component | Count | Configuration |
|----------|----------|----------|
| Control Plane Nodes | 3 | 4 CPU, 8 GB RAM, 50 GB Storage |
| Worker Nodes (General) | 3-6 | 4 CPU, 16 GB RAM, 100 GB Storage |
| Worker Nodes (Compute) | 2-4 | 8 CPU, 32 GB RAM, 100 GB Storage |
| Worker Nodes (Storage) | 2-4 | 4 CPU, 16 GB RAM, 500 GB Storage |

**Add-on Components**:

| Add-on | Purpose | Namespace |
|----------|----------|----------|
| NSX-T Container Plugin | Container networking | kube-system |
| vSAN CSI Driver | Persistent volumes | kube-system |
| NSX-T Ingress Controller | Ingress management | nsx-system |
| Prometheus Operator | Metrics collection | monitoring |
| Grafana | Metrics visualization | monitoring |
| Fluent Bit | Log collection | logging |
| Loki | Log aggregation | logging |
| Vault Agent Injector | Secrets injection | security |
| cert-manager | Certificate management | cert-manager |

### 7.1.3 Container Runtime Configuration

**Container Runtime**: containerd

**Configuration**:
```
[plugins."io.containerd.grpc.v1.cri"]
  sandbox_image = "registry.k8s.io/pause:3.9"
  
[plugins."io.containerd.grpc.v1.cri".containerd]
  default_runtime_name = "runc"
  
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  runtime_engine = "runc"
  runtime_root = ""
```

**Image Registry**:
- Primary: VMware Harbor Registry (Private)
- Secondary: Docker Hub (Public)
- Tertiary: Quay.io (Public)

### 7.1.4 Workload Deployment Patterns

**Function**: `execute_platform_workflow(workflow_name: str) -> bool`

```python
def execute_platform_workflow(workflow_name):
    """
    Executes platform automation workflows
    
    Supported Workflows:
    - provision_vm: Deploy new virtual machine
    - deploy_kubernetes: Deploy Kubernetes cluster
    - configure_networking: Configure network segments
    - enable_backup: Enable backup for workload
    - failover_workload: Failover to DR site
    - patch_infrastructure: Apply patches
    - scale_cluster: Scale cluster up/down
    """
    # Retrieves workflow definition
    # Validates input parameters
    # Executes workflow steps
    # Monitors execution
    # Returns workflow status
```

**Workflow Execution Engine**: Aria Orchestrator

---

## 7.2 Storage Design

### 7.2.1 vSAN Software-Defined Storage

**vSAN Cluster Configuration**:

| Parameter | Value | Rationale |
|----------|----------|----------|
| Deduplication | Enabled | Reduce storage footprint |
| Compression | Enabled | Improve capacity efficiency |
| Encryption | Enabled | Data protection at rest |
| Replication | 2 copies | High availability |
| Failure Tolerance | 1 node | Tolerate single node failure |
| Stripe Width | 2 | Balance performance and resilience |

**vSAN Disk Groups**:

```
Per ESXi Host:
├── Cache Tier (SSD)
│   ├── 2x 400 GB NVMe SSDs
│   └── Caching layer for hot data
└── Capacity Tier (HDD)
    ├── 4x 4 TB SAS HDDs
    └── Primary storage for workloads
```

**Storage Policy Framework**:

| Policy | RAID | Replicas | Stripe | Use Case |
|----------|----------|----------|----------|----------|
| Gold | RAID-1 | 2 | 2 | Critical workloads, databases |
| Silver | RAID-5 | 1 | 2 | Standard workloads, VMs |
| Bronze | RAID-6 | 1 | 3 | Non-critical, development |

**Capacity Planning**:

| Component | Capacity | Utilization Target |
|----------|----------|----------|
| Total vSAN Capacity | 100 TB | 70% |
| Reserved for Replication | 30 TB | N/A |
| Available for Workloads | 70 TB | 70% |
| Usable Capacity | 49 TB | N/A |

### 7.2.2 Persistent Volume Management

**vSAN CSI Driver Configuration**:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: vsan-default
provisioner: csi.vsphere.vmware.com
parameters:
  storagepolicyname: "Silver"
  fstype: "ext4"
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

**Persistent Volume Types**:

| Type | Capacity | Performance | Use Case |
|----------|----------|----------|----------|
| Standard | 10-100 GB | Medium | General applications |
| High-Performance | 10-100 GB | High | Databases, analytics |
| High-Capacity | 100-1000 GB | Medium | Data warehouses, archives |

### 7.2.3 Backup Storage Architecture

**Backup Infrastructure**:

```
┌─────────────────────────────────────────────────────────────┐
│              Backup & Recovery Infrastructure                │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Canopy Enterprise Backup (Backup Manager)           │   │
│  │  ├─ Backup Policy Management                         │   │
│  │  ├─ Job Scheduling                                   │   │
│  │  ├─ Retention Management                             │   │
│  │  └─ Recovery Orchestration                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Avamar (Backup Engine)                              │   │
│  │  ├─ VM Backup                                        │   │
│  │  ├─ Application Backup                               │   │
│  │  ├─ Database Backup                                  │   │
│  │  └─ File System Backup                               │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Data Domain (Backup Storage)                        │   │
│  │  ├─ Deduplication Storage                            │   │
│  │  ├─ Compression                                      │   │
│  │  ├─ Replication                                      │   │
│  │  └─ Retention Policies                               │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

**Backup Policies**:

| Policy | Frequency | Retention | RPO | RTO |
|----------|----------|----------|----------|----------|
| Critical | Hourly | 30 days | 1 hour | 15 minutes |
| Standard | Daily | 90 days | 24 hours | 1 hour |
| Archive | Weekly | 1 year | 7 days | 4 hours |

**Function**: `schedule_backup_job(workload_name: str) -> bool`

```python
def schedule_backup_job(workload_name):
    """
    Schedules backup job for workload
    
    Process:
    1. Validate workload exists
    2. Determine backup policy
    3. Create backup schedule
    4. Configure retention
    5. Enable deduplication
    6. Register with monitoring
    """
    # Connects to Canopy Backup Manager
    # Creates backup job
    # Sets schedule
    # Configures storage
    # Returns job ID
```

---

## 7.3 Network Design

### 7.3.1 NSX-T Network Virtualization

**NSX-T Architecture**:

```
┌─────────────────────────────────────────────────────────────┐
│                    NSX-T Management Plane                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  NSX-T Manager Cluster (3 Nodes)                     │   │
│  │  ├─ API Server                                       │   │
│  │  ├─ Policy Engine                                    │   │
│  │  ├─ Audit Logging                                    │   │
│  │  └─ Certificate Management                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  NSX-T Controller Cluster (3 Nodes)                  │   │
│  │  ├─ Logical Switch Control                           │   │
│  │  ├─ MAC Learning                                     │   │
│  │  ├─ ARP Suppression                                  │   │
│  │  └─ DHCP Relay                                       │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    NSX-T Data Plane                          │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  NSX-T Kernel Module (On Each ESXi Host)             │   │
│  │  ├─ Virtual Switch (vDS)                             │   │
│  │  ├─ Distributed Firewall                             │   │
│  │  ├─ Logical Router                                   │   │
│  │  └─ Tunnel Endpoint (VTEP)                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  NSX-T Edge Nodes (2-3 Nodes)                        │   │
│  │  ├─ Tier-0 Gateway                                   │   │
│  │  ├─ Tier-1 Gateway                                   │   │
│  │  ├─ NAT Services                                     │   │
│  │  ├─ Load Balancing                                   │   │
│  │  ├─ VPN Services                                     │   │
│  │  └─ Firewall Services                                │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

**Function**: `deploy_network_foundation(region: str) -> bool`

```python
def deploy_network_foundation(region):
    """
    Deploys core networking components for a new cloud platform
    
    Process:
    1. Deploy NSX-T Manager cluster
    2. Deploy NSX-T Controller cluster
    3. Deploy NSX-T Edge nodes
    4. Configure Tier-0 gateway
    5. Configure Tier-1 gateways
    6. Create logical switches
    7. Configure firewall rules
    8. Configure routing
    9. Validate connectivity
    """
    # Connects to vCenter
    # Deploys NSX-T VMs
    # Configures NSX-T cluster
    # Creates network segments
    # Configures gateways
    # Returns deployment status
```

### 7.3.2 Logical Network Segments

**Segment Configuration**:

| Segment | VLAN | Subnet | Gateway | DHCP | Purpose |
|----------|----------|----------|----------|----------|----------|
| Management | 100 | 10.0.1.0/24 | 10.0.1.1 | Yes | Management traffic |
| vMotion | 101 | 10.0.2.0/24 | 10.0.2.1 | No | VM migration |
| vSAN | 102 | 10.0.3.0/24 | 10.0.3.1 | No | Storage traffic |
| Kubernetes | 104 | 10.0.4.0/24 | 10.0.4.1 | Yes | Kubernetes pods |
| Tenant-A | 110 | 10.1.0.0/24 | 10.1.0.1 | Yes | Tenant A workloads |
| Tenant-B | 111 | 10.2.0.0/24 | 10.2.0.1 | Yes | Tenant B workloads |
| Backup | 105 | 10.0.5.0/24 | 10.0.5.1 | No | Backup traffic |
| DR | 106 | 10.0.6.0/24 | 10.0.6.1 | No | DR replication |

**Segment Policies**:

```yaml
# Management Segment
- Name: Management
  Type: Overlay
  Replication Mode: Source
  DHCP: Enabled
  DNS: 8.8.8.8, 8.8.4.4
  Domain: cloud.local
  
# Tenant Segment
- Name: Tenant-A
  Type: Overlay
  Replication Mode: Source
  DHCP: Enabled
  DNS: 10.0.1.10
  Domain: tenant-a.cloud.local
  Firewall: Enabled
  QoS: Enabled (1 Gbps limit)
```

### 7.3.3 Routing Architecture

**Tier-0 Gateway Configuration**:

```
Tier-0 Gateway (Active-Active)
├── Uplink Interfaces
│   ├─ Uplink-1 (10.0.0.1/24 on VLAN 100)
│   └─ Uplink-2 (10.0.0.2/24 on VLAN 100)
├── BGP Configuration
│   ├─ Local AS: 65000
│   ├─ Remote AS: 65001 (Core Router)
│   └─ Route Redistribution: Enabled
├── HA Configuration
│   ├─ HA Mode: Active-Active
│   └─ Failover: Automatic
└── Services
    ├─ NAT (for external connectivity)
    ├─ Load Balancing
    └─ VPN (for site-to-site)
```

**Tier-1 Gateway Configuration**:

```
Tier-1 Gateway (Per Tenant)
├── Connected to Tier-0
├── Downlink Interfaces
│   └─ Connected to Logical Switches
├── DHCP Relay
│   └─ Relay to DHCP Server (10.0.1.10)
├── Firewall Rules
│   ├─ Tenant Isolation
│   ├─ Application Security
│   └─ Compliance Rules
└── Services
    ├─ NAT (for internal connectivity)
    └─ Load Balancing
```

### 7.3.4 Network Security Zones

**Security Zone Architecture**:

```
┌─────────────────────────────────────────────────────────────┐
│                    DMZ Zone                                  │
│  ├─ External-facing services                                │
│  ├─ Load balancers                                           │
│  └─ API gateways                                             │
└─────────────────────────────────────────────────────────────┘
                         │
                    Firewall Rules
                         │
┌─────────────────────────────────────────────────────────────┐
│                    Application Zone                          │
│  ├─ Application servers                                      │
│  ├─ Web servers                                              │
│  └─ Middleware                                               │
└─────────────────────────────────────────────────────────────┘
                         │
                    Firewall Rules
                         │
┌─────────────────────────────────────────────────────────────┐
│                    Data Zone                                 │
│  ├─ Databases                                                │
│  ├─ Data warehouses                                          │
│  └─ Storage systems                                          │
└─────────────────────────────────────────────────────────────┘
                         │
                    Firewall Rules
                         │
┌─────────────────────────────────────────────────────────────┐
│                    Management Zone                           │
│  ├─ vCenter                                                  │
│  ├─ NSX-T Manager                                            │
│  ├─ Aria Automation                                          │
│  └─ Monitoring systems                                       │
└─────────────────────────────────────────────────────────────┘
```

### 7.3.5 Distributed Firewall Rules

**Firewall Policy Framework**:

| Policy | Source | Destination | Service | Action | Priority |
|----------|----------|----------|----------|----------|----------|
| Allow-Management | Management Zone | All | SSH, HTTPS | Allow | 100 |
| Allow-App-to-DB | Application Zone | Data Zone | MySQL, PostgreSQL | Allow | 200 |
| Allow-Tenant-Internal | Tenant-A | Tenant-A | All | Allow | 300 |
| Deny-Tenant-Cross | Tenant-A | Tenant-B | All | Deny | 400 |
| Allow-Internet | DMZ | External | HTTP, HTTPS | Allow | 500 |
| Deny-All | Any | Any | Any | Deny | 65000 |

---

## 7.4 Platform Configuration

### 7.4.1 vSphere Configuration

**vCenter Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| SSO Domain | vsphere.local | Single Sign-On domain |
| NTP Servers | 10.0.1.10, 10.0.1.11 | Time synchronization |
| DNS Servers | 8.8.8.8, 8.8.4.4 | Name resolution |
| Search Domains | cloud.local | DNS search path |
| Syslog Server | 10.0.1.20 | Centralized logging |
| SNMP Community | public | SNMP monitoring |

**ESXi Host Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| NTP Servers | 10.0.1.10, 10.0.1.11 | Time synchronization |
| DNS Servers | 8.8.8.8, 8.8.4.4 | Name resolution |
| Syslog Server | 10.0.1.20 | Centralized logging |
| SSH Enabled | Yes | Remote management |
| Firewall | Enabled | Network security |
| Lockdown Mode | Strict | Security hardening |

**vSphere Cluster Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| DRS | Enabled | Load balancing |
| DRS Automation Level | Fully Automated | Automatic VM placement |
| HA | Enabled | High availability |
| HA Admission Control | Enabled | Resource reservation |
| vSAN | Enabled | Distributed storage |
| vSAN Deduplication | Enabled | Storage efficiency |
| vSAN Compression | Enabled | Storage efficiency |
| vSAN Encryption | Enabled | Data protection |

### 7.4.2 NSX-T Configuration

**NSX-T Manager Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| Cluster Size | 3 nodes | High availability |
| API Timeout | 300 seconds | API responsiveness |
| Audit Logging | Enabled | Security auditing |
| Certificate Validation | Enabled | Security |
| Backup Schedule | Daily | Disaster recovery |

**NSX-T Edge Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| Cluster Size | 2-3 nodes | High availability |
| CPU Reservation | 4 cores | Performance |
| Memory Reservation | 8 GB | Performance |
| Storage | 50 GB | Local storage |
| HA Mode | Active-Active | Load balancing |

### 7.4.3 Aria Automation Configuration

**Aria Automation Cluster**:

| Component | Count | Configuration |
|----------|----------|----------|
| Aria Automation Appliances | 3 | 4 CPU, 16 GB RAM, 100 GB Storage |
| Aria Orchestrator Appliances | 3 | 4 CPU, 16 GB RAM, 100 GB Storage |
| Aria Operations Appliances | 3 | 8 CPU, 32 GB RAM, 200 GB Storage |

**Aria Automation Configuration**:

| Parameter | Value | Purpose |
|----------|----------|----------|
| Cloud Accounts | vSphere, NSX-T, Kubernetes | Infrastructure integration |
| Approval Policy | Enabled | Change control |
| Notification | Email, Slack | Alerting |
| Backup | Daily | Disaster recovery |

---

## 7.5 Application / Service Components

### 7.5.1 Core Platform Services

| Component | Purpose | Dependencies | Deployment |
|----------|----------|----------|----------|
| vCenter | Infrastructure management | vSphere | VM |
| NSX-T Manager | Network management | vSphere | VM |
| NSX-T Controller | Network control plane | vSphere | VM |
| NSX-T Edge | Network data plane | vSphere | VM |
| Aria Automation | Provisioning orchestration | vSphere, NSX-T | VM |
| Aria Orchestrator | Workflow automation | vSphere | VM |
| Aria Operations | Monitoring | vSphere, NSX-T, vSAN | VM |
| Aria Logs | Log aggregation | vSphere | VM |
| SDDC Manager | Lifecycle management | vSphere, NSX-T | VM |
| HashiCorp Vault | Secrets management | vSphere | VM |

### 7.5.2 Kubernetes Platform Services

| Component | Purpose | Namespace | Deployment |
|----------|----------|----------|----------|
| TKG Control Plane | Kubernetes management | kube-system | VM |
| TKG Worker Nodes | Workload hosting | kube-system | VM |
| NSX-T CNI | Container networking | kube-system | DaemonSet |
| vSAN CSI | Persistent volumes | kube-system | StatefulSet |
| NSX-T Ingress | Ingress management | nsx-system | Deployment |
| Prometheus | Metrics collection | monitoring | StatefulSet |
| Grafana | Metrics visualization | monitoring | Deployment |
| Fluent Bit | Log collection | logging | DaemonSet |
| Loki | Log aggregation | logging | StatefulSet |
| Vault Agent | Secrets injection | security | DaemonSet |

### 7.5.3 Backup & DR Services

| Component | Purpose | Deployment | Configuration |
|----------|----------|----------|----------|
| Canopy Backup Manager | Backup orchestration | VM | 4 CPU, 16 GB RAM |
| Avamar | Backup engine | VM | 8 CPU, 32 GB RAM |
| Data Domain | Backup storage | Appliance | 100 TB capacity |
| SRM | Disaster recovery | VM | 4 CPU, 8 GB RAM |
| vSphere Replication | VM replication | Kernel module | Enabled on all hosts |

### 7.5.4 Security Services

| Component | Purpose | Deployment | Configuration |
|----------|----------|----------|----------|
| HashiCorp Vault | Secrets management | VM | 4 CPU, 8 GB RAM |
| Trend Micro | Endpoint protection | VM Agent | Installed on all VMs |
| Nessus | Vulnerability scanning | VM | 4 CPU, 8 GB RAM |
| Vault Agent | Secrets injection | Kubernetes | DaemonSet |
| cert-manager | Certificate management | Kubernetes | Deployment |

---

# 8. Data Design

## 8.1 Data Flow

### 8.1.1 Provisioning Data Flow

```
User Request
    │
    ▼
Service Broker Portal
    │
    ├─ Validate Request
    ├─ Check Quotas
    └─ Retrieve Catalog
    │
    ▼
Aria Automation
    │
    ├─ Retrieve Credentials from Vault
    ├─ Connect to vSphere API
    ├─ Connect to NSX-T API
    └─ Connect to vSAN API
    │
    ▼
Infrastructure Provisioning
    │
    ├─ Create VM from Template
    ├─ Configure vNIC
    ├─ Allocate Storage
    └─ Apply Security Policies
    │
    ▼
Post-Deployment Configuration
    │
    ├─ Apply Baseline Configuration
    ├─ Register with Monitoring
    ├─ Enable Backup
    └─ Configure Logging
    │
    ▼
Deployment Complete
    │
    └─ Return Access Credentials
```

### 8.1.2 Monitoring Data Flow

```
Infrastructure Components
    │
    ├─ vSphere (Metrics)
    ├─ NSX-T (Metrics)
    ├─ vSAN (Metrics)
    ├─ Kubernetes (Metrics)
    └─ Applications (Metrics)
    │
    ▼
Aria Operations Collector
    │
    ├─ Collect Metrics
    ├─ Collect Events
    └─ Collect Logs
    │
    ▼
Aria Operations Analytics
    │
    ├─ Aggregate Data
    ├─ Analyze Trends
    ├─ Detect Anomalies
    └─ Generate Alerts
    │
    ▼
Aria Logs Aggregation
    │
    ├─ Parse Logs
    ├─ Index Logs
    └─ Store Logs
    │
    ▼
Dashboards & Reports
    │
    ├─ Real-time Dashboards
    ├─ Historical Reports
    └─ Compliance Reports
```

### 8.1.3 Backup Data Flow

```
Workload Data
    │
    ▼
Backup Agent (Avamar)
    │
    ├─ Snapshot Data
    ├─ Deduplicate
    └─ Compress
    │
    ▼
Data Domain Storage
    │
    ├─ Store Backup
    ├─ Replicate to DR Site
    └─ Archive to Cloud
    │
    ▼
Backup Catalog
    │
    ├─ Index Backup
    ├─ Track Retention
    └─ Schedule Cleanup
    │
    ▼
Recovery Process
    │
    ├─ Retrieve Backup
    ├─ Decompress
    └─ Restore Data
```

## 8.2 Data Storage

### 8.2.1 Storage Tiers

**Tier 1: Hot Storage (vSAN SSD Cache)**
- Capacity: 10 TB
- Performance: < 5ms latency
- Retention: Active data only
- Use Case: Real-time workloads

**Tier 2: Warm Storage (vSAN HDD)**
- Capacity: 50 TB
- Performance: 10-50ms latency
- Retention: 30 days
- Use Case: Standard workloads

**Tier 3: Cold Storage (Data Domain)**
- Capacity: 100 TB
- Performance: 100-500ms latency
- Retention: 1 year
- Use Case: Archives, compliance

**Tier 4: Archive Storage (Cloud)**
- Capacity: Unlimited
- Performance: > 1 second latency
- Retention: 7 years
- Use Case: Long-term retention

### 8.2.2 Data Replication Strategy

**Synchronous Replication**:
- RPO: 0 (zero data loss)
- RTO: < 1 minute
- Use Case: Critical databases
- Implementation: vSphere Replication

**Asynchronous Replication**:
- RPO: 1 hour
- RTO: < 4 hours
- Use Case: Standard workloads
- Implementation: SRM with vSphere Replication

**Backup-based Recovery**:
- RPO: 24 hours
- RTO: < 8 hours
- Use Case: Non-critical workloads
- Implementation: Canopy Backup

## 8.3 Database Objects

### 8.3.1 Aria Automation Database

**Schema**: PostgreSQL

| Table | Purpose | Retention |
|----------|----------|----------|
| requests | Provisioning requests | 1 year |
| deployments | Deployment records | 1 year |
| resources | Resource inventory | Indefinite |
