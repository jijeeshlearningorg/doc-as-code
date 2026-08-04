# Operations Guide (OPG): my-cloud-platform

**Author:** Operations Architecture Team  
**Date:** 2024  
**Version:** 1.0  
**Status:** Final  
**Owner:** Platform Operations Team  

---

# 1. Document Control

## 1.1 Distribution & Approval

| Role | Name | Approval Status | Approval Date |
|----------|----------|----------|----------|
| Service Owner | Cloud Platform Director | Pending | |
| Operations Manager | Infrastructure Operations Lead | Pending | |
| Platform Owner | VCS Platform Lead | Pending | |
| Security Representative | Security & Compliance Officer | Pending | |
| Support Lead | L3 Support Manager | Pending | |

---

## 1.2 Review History

| Reviewer | Role | Date | Comments |
|----------|----------|----------|----------|
| Operations Architecture | Senior Architect | 2024 | Initial creation |

---

## 1.3 Change Log

| Version | Date | Description | Author |
|----------|----------|----------|----------|
| 1.0 | 2024 | Initial Operations Guide | Operations Architecture Team |

---

# 2. Related Documents

| Document Type | Reference | Relationship |
|----------|----------|----------|
| HLD | my-cloud-platform High-Level Design | Architecture foundation |
| LLD | my-cloud-platform Low-Level Design | Detailed implementation |
| BIG | my-cloud-platform Build & Installation Guide | Deployment procedures |
| OPG | Current Document | Operational procedures |
| ADR | Architecture Decision Records | Design rationale |
| Runbooks | Operational Runbooks | Step-by-step procedures |

---

# 3. Service Overview

## 3.1 Service Purpose

my-cloud-platform is a comprehensive VMware Cloud Foundation-based infrastructure platform providing integrated compute, storage, networking, and automation services. The platform delivers:

- **Compute Services**: VMware vSphere-based virtual machine hosting with resource management and lifecycle automation
- **Storage Services**: Software-defined storage via VMware vSAN with optional Fibre Channel integration
- **Networking Services**: NSX-T based virtual networking, routing, segmentation and multi-tenant connectivity
- **Automation Services**: Aria Automation-driven provisioning, orchestration and self-service delivery
- **Kubernetes Services**: Tanzu Kubernetes Grid platform for containerized workloads
- **Disaster Recovery**: Site protection, workload replication and recovery capabilities
- **Backup Services**: Image and application-level backup with enterprise recovery options
- **Security Services**: Platform security controls, vault-based secrets management and vulnerability management
- **Monitoring & Observability**: Aria Operations-based performance monitoring, Aria Logs for centralized logging
- **Multi-Tenancy**: Logical and operational separation supporting multiple business units and customers
- **Public Cloud Integration**: Connectivity and workload integration with hyperscaler environments
- **Reporting**: Operational, utilization and billing analytics

The platform serves as the foundational infrastructure layer supporting enterprise applications, modern containerized workloads, and hybrid cloud operations.

---

## 3.2 Business Criticality

**Mission Critical**

my-cloud-platform is classified as Mission Critical infrastructure supporting:
- Production application workloads
- Business-critical services
- Revenue-generating operations
- Regulatory compliance requirements

Availability targets: 99.99% uptime (52.6 minutes maximum downtime per year)

---

## 3.3 Supported Environments

- **Development**: Non-production development and testing
- **Test**: Quality assurance and integration testing
- **UAT**: User acceptance testing and pre-production validation
- **Production**: Live business operations and customer-facing services

---

## 3.4 Operational Scope

### In Scope

- Platform monitoring and observability
- Infrastructure patching and lifecycle management
- Backup execution and recovery validation
- Incident detection and response
- Capacity planning and resource optimization
- Security operations and compliance monitoring
- Disaster recovery testing and failover execution
- Service request fulfillment
- Performance optimization
- Configuration baseline maintenance

### Out of Scope

- Application development activities
- Architecture governance and design decisions
- Major platform enhancements and feature development
- Vendor product development
- Customer application support (application teams responsible)
- Network infrastructure outside platform scope

---

# 4. Service Ownership

## 4.1 Ownership Matrix

| Function | Owner |
|----------|----------|
| Service Owner | Cloud Platform Director |
| Technical Owner | VCS Platform Lead |
| Operations Team | Infrastructure Operations Team |
| Support Team | L1/L2/L3 Support Teams |
| Security Team | Security & Compliance Team |
| Backup Operations | Backup & Recovery Team |
| Disaster Recovery | DR Operations Team |
| Monitoring & Observability | Observability Team |
| Automation & Orchestration | Automation Engineering Team |
| Vendor Management | VMware Account Manager |

---

## 4.2 Support Model

| Level | Responsibility |
|----------|----------|
| L1 | Initial incident triage, alert acknowledgment, basic troubleshooting, ticket routing |
| L2 | Advanced troubleshooting, configuration changes, runbook execution, escalation to L3 |
| L3 | Deep technical investigation, architecture-level issues, vendor escalation, root cause analysis |
| Vendor | VMware product support, critical defects, architectural guidance, emergency support |

---

## 4.3 Escalation Path

| Severity | Escalation Contact | Response Time | Resolution Target |
|----------|----------|----------|----------|
| P1 - Critical | L3 Support Manager → Platform Owner → Service Owner | 15 minutes | 4 hours |
| P2 - High | L2 Support Lead → L3 Support Manager | 30 minutes | 8 hours |
| P3 - Medium | L2 Support Team → L2 Support Lead | 2 hours | 24 hours |
| P4 - Low | L1 Support Team → L2 Support Lead | 8 hours | 5 business days |

---

# 5. Operational Principles

## 5.1 Approved Change Mechanisms

All production changes to my-cloud-platform shall be performed using approved change processes:

- **Infrastructure-as-Code**: All infrastructure changes via version-controlled code repositories
- **CI/CD Pipelines**: Automated deployment pipelines with approval gates
- **GitOps Workflows**: Git-driven infrastructure synchronization
- **Change Advisory Board**: CAB approval for high-risk changes
- **Maintenance Windows**: Scheduled change windows for non-emergency modifications
- **Emergency Change Process**: Expedited approval for critical incidents

Changes must include:
- Change description and business justification
- Risk assessment and mitigation
- Rollback procedure
- Testing evidence
- Approval documentation

---

## 5.2 Configuration Management Principles

- **Everything as Code**: All configuration stored in version control
- **Automated Deployment**: Infrastructure provisioning via automation workflows
- **Version Controlled Configuration**: Git-based configuration management
- **Automated Rollback**: Automated reversion to previous known-good state
- **Immutable Infrastructure**: Rebuild rather than patch where possible
- **Configuration Baseline**: Standard baseline configurations for all components
- **Drift Detection**: Automated detection of configuration deviations
- **Audit Trail**: Complete change history and accountability

---

## 5.3 Operational Restrictions

### Supported Activities

- Service restart via approved procedures
- Deployment approval and execution
- Published runbook execution
- Alert investigation and response
- Capacity increase requests
- Backup and recovery operations
- Configuration baseline deployment
- Performance optimization
- Security patching within maintenance windows

### Restricted Activities

- Manual production reconfiguration outside change process
- Direct infrastructure modification bypassing automation
- Bypass of deployment pipelines and approval gates
- Untracked or undocumented changes
- Direct database modifications
- Credential sharing or hardcoding
- Disabling monitoring or alerting
- Extending maintenance windows without approval
- Vendor product modification

---

## 5.4 Break Glass Procedures

### Emergency Access

In critical incidents where normal change processes would cause unacceptable business impact:

1. **Incident Declaration**: Service Owner declares emergency status
2. **Immediate Action**: Execute emergency remediation with verbal approval
3. **Documentation**: Record all actions taken with timestamps and justification
4. **Post-Incident Review**: Conduct RCA within 24 hours
5. **Change Retroactive Approval**: CAB reviews and approves emergency changes within 48 hours
6. **Process Improvement**: Implement preventive measures to avoid recurrence

Emergency access credentials stored in secure vault with:
- Dual control (two authorized personnel required)
- Audit logging of all access
- Automatic session recording
- Time-limited access (maximum 4 hours)
- Mandatory post-incident review

---

# 6. Monitoring & Observability

## 6.1 Monitoring Requirements

| Metric | Component | Threshold | Alert Required | Monitoring Tool |
|----------|----------|----------|----------|----------|
| CPU Utilization | ESXi Hosts | >85% sustained | Yes | Aria Operations |
| Memory Utilization | ESXi Hosts | >90% sustained | Yes | Aria Operations |
| Storage Capacity | vSAN Datastore | >80% | Yes | Aria Operations |
| Storage I/O Latency | vSAN | >20ms | Yes | Aria Operations |
| Network Bandwidth | NSX-T | >80% link capacity | Yes | Aria Network Insight |
| VM Availability | Virtual Machines | <99.9% | Yes | Aria Operations |
| vCenter Availability | Management | <99.99% | Yes | Aria Operations |
| NSX Manager Availability | Networking | <99.99% | Yes | Aria Operations |
| Backup Success Rate | Backup | <99% | Yes | Canopy/Avamar |
| Backup Window Duration | Backup | >SLA window | Yes | Canopy/Avamar |
| Replication Lag | DR | >RPO threshold | Yes | vSphere Replication |
| API Response Time | Service Broker | >2 seconds | Yes | Aria Operations |
| Automation Workflow Success | Automation | <95% | Yes | Aria Automation |
| Certificate Expiration | Security | <30 days | Yes | Aria Operations |
| Vault Health | Security | Unhealthy | Yes | Aria Operations |
| Log Ingestion Rate | Logging | >10% deviation | Yes | Aria Logs |
| Vulnerability Scan Status | Security | Overdue | Yes | Nessus/Aria Operations |

---

## 6.2 Dashboards

| Dashboard | Purpose | Audience | Refresh Rate |
|----------|----------|----------|----------|
| Platform Health Overview | Real-time platform status and key metrics | Operations Team, Management | 1 minute |
| Compute Capacity | CPU, memory, storage utilization trends | Capacity Planning Team | 5 minutes |
| Storage Performance | vSAN performance, latency, throughput | Storage Team | 1 minute |
| Network Utilization | NSX-T bandwidth, segment health | Network Team | 1 minute |
| Backup Status | Backup job success, duration, capacity | Backup Team | 5 minutes |
| Disaster Recovery | Replication lag, recovery readiness | DR Team | 5 minutes |
| Security Posture | Vulnerability status, compliance metrics | Security Team | 1 hour |
| Automation Workflows | Workflow execution, success rates | Automation Team | 5 minutes |
| Multi-Tenant Isolation | Per-tenant resource usage, isolation | Tenant Management | 5 minutes |
| API Service Broker | API availability, response times, usage | Service Broker Team | 1 minute |
| Kubernetes Cluster Health | Tanzu cluster status, node health | Container Team | 1 minute |
| Incident Timeline | Active incidents, resolution status | Operations Team | Real-time |

---

## 6.3 Alerting

| Alert | Severity | Threshold | Response Target | Escalation |
|----------|----------|----------|----------|----------|
| ESXi Host Down | P1 | Host unreachable | 5 minutes | L3 → Platform Owner |
| vCenter Unavailable | P1 | Service down | 5 minutes | L3 → Platform Owner |
| NSX Manager Cluster Degraded | P1 | <3 managers healthy | 5 minutes | L3 → Platform Owner |
| vSAN Cluster Unhealthy | P1 | Cluster degraded | 5 minutes | L3 → Platform Owner |
| Storage Capacity Critical | P1 | >95% utilized | 15 minutes | L2 → L3 → Storage Team |
| Backup Job Failed | P2 | Job failure | 30 minutes | L2 → Backup Team |
| Replication Lag Exceeded | P2 | >RPO threshold | 30 minutes | L2 → DR Team |
| High CPU Utilization | P2 | >85% sustained 10min | 30 minutes | L2 → Capacity Team |
| High Memory Utilization | P2 | >90% sustained 10min | 30 minutes | L2 → Capacity Team |
| Network Link Degraded | P2 | >80% utilization | 30 minutes | L2 → Network Team |
| Certificate Expiring Soon | P3 | <30 days | 1 hour | L2 → Security Team |
| Vulnerability Scan Overdue | P3 | >30 days | 2 hours | L2 → Security Team |
| Automation Workflow Failure | P3 | Workflow failed | 1 hour | L2 → Automation Team |
| API Response Time High | P3 | >2 seconds | 1 hour | L2 → Service Broker Team |
| Log Ingestion Lag | P3 | >5 minute lag | 2 hours | L2 → Observability Team |
| Vault Seal Status | P2 | Vault sealed | 30 minutes | L2 → L3 → Security Team |
| Kubernetes Node Not Ready | P2 | Node unhealthy | 30 minutes | L2 → Container Team |
| Tenant Resource Quota Exceeded | P3 | >90% quota | 2 hours | L2 → Tenant Management |

---

## 6.4 Logging

### Application Logs

- **Aria Automation**: Workflow execution logs, provisioning events, orchestration activities
- **Aria Orchestrator**: Workflow logs, script execution, integration events
- **Service Broker**: API requests, service catalog operations, subscription events
- **Automation Scripts**: Custom automation execution logs, error conditions

**Retention**: 90 days hot, 1 year archive

### Platform Logs

- **vCenter**: Virtual machine events, cluster operations, resource allocation
- **ESXi Hosts**: Host events, VM lifecycle, resource utilization
- **vSAN**: Storage operations, rebalancing, health events
- **NSX-T**: Network operations, segment creation, routing events
- **SDDC Manager**: Lifecycle operations, cluster management, updates

**Retention**: 30 days hot, 1 year archive

### Infrastructure Logs

- **Backup Systems**: Canopy, Avamar job logs, backup operations
- **Disaster Recovery**: SRM, vSphere Replication logs, failover events
- **Security**: Vault operations, key rotation, policy changes
- **Monitoring**: Aria Operations, Aria Logs, alert generation

**Retention**: 30 days hot, 90 days warm, 1 year cold

### Security Logs

- **Access Logs**: User authentication, authorization, privilege escalation
- **Audit Logs**: Configuration changes, policy modifications, administrative actions
- **Vulnerability Logs**: Nessus scan results, remediation tracking
- **Threat Logs**: Security events, anomalies, incident indicators

**Retention**: 1 year hot, 3 years archive (compliance requirement)

---

## 6.5 Audit Logging

### Audit Events

| Event Category | Events Logged | Retention |
|----------|----------|----------|
| User Access | Login, logout, failed authentication, privilege escalation | 1 year |
| Configuration Changes | Infrastructure changes, policy modifications, baseline updates | 1 year |
| Data Access | Backup access, recovery operations, data export | 1 year |
| Security Operations | Vault operations, key rotation, certificate management | 3 years |
| Compliance Operations | Audit activities, evidence collection, report generation | 3 years |
| Incident Response | Incident creation, escalation, resolution | 1 year |
| Disaster Recovery | Failover execution, recovery testing, RTO/
