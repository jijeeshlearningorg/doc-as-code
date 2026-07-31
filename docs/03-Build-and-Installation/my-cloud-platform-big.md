# Build & Installation Guide (BIG): My Cloud Services

**Author:**  
**Date:** 2026-07-31  
**Version:** 1.0  
**Status:** Draft  
**Owner:** TBD  

---

# 1. Document Control

## 1.1 Review & Approval

| Role | Name | Status |
|------|------|--------|
| Reviewer | | |
| Security Review | | |
| Document Owner | | |

## 1.2 Change Log

| Version | Date | Description | Author |
|---------|------|-------------|--------|
| 1.0 | 2026-07-31 | Initial draft | |

---

# 2. Introduction

## 2.1 Purpose

This document provides the detailed steps required to build, install, configure, validate, hand over, and support the **My Cloud Services** platform, which includes AI, data, Kubernetes, and networking foundations built on VMware NSX‑T and Aria Network Insight.

## 2.2 Audience

- Platform Engineers  
- Operations Teams  
- Support Teams  
- Security Teams  

## 2.3 Scope

### In Scope

- Installation  
- Configuration  
- Validation  
- Handover  

### Out of Scope

- High‑Level Design (HLD)  
- Low‑Level Design (LLD)  
- Operational Procedures (OPG)  

## 2.4 Related Documents

| Document Type | Document ID | Relationship |
|---------------|-------------|--------------|
| HLD | | Architecture Design |
| LLD | | Detailed Design |
| BIG | | Current Document |
| OPG | | Operations Guide |
| ADR | | Architecture Decisions |
| Runbooks | | Operational Procedures |
| Vendor Documentation | | Product Reference |

---

# 3. Deployment Context

- **System Type:** Cloud Platform  
- **Deployment Model:** Private Cloud (VMware SDDC)  
- **Platform/Provider:** VMware vSphere, NSX‑T, Aria Suite  
- **Environment:** Production  

---

# 4. Package / Build Description

## 4.1 Package Overview

The **My Cloud Services** package deploys a multi‑tenant cloud platform comprising AI, data, Kubernetes, and networking services. It leverages VMware NSX‑T for software‑defined networking and Aria Network Insight for network visibility.

## 4.2 Product / Platform Components

| Component | Source / Location |
|-----------|-------------------|
| vCenter Server | `https://vcenter.example.com` |
| ESXi Hosts | `esxi-host-01` to `esxi-host-04` |
| NSX‑T Manager | `https://nsxt-manager.example.com` |
| NSX‑T Edge | `nsxt-edge-01` to `nsxt-edge-02` |
| Aria Network Insight | `https://aria-insight.example.com` |
| Aria Automation | `https://aria-automation.example.com` |
| Aria Orchestrator | `https://aria-orchestrator.example.com` |
| Aria Operations | `https://aria-operations.example.com` |
| Aria Logs | `https://aria-logs.example.com` |
| Aria Suite Lifecycle Manager | `https://lifecycle-manager.example.com` |
| GitHub Actions | `https://github.com/jijeeshlearningorg/greenfield-code` |
| Ansible | `ansible` playbooks in repo |
| Terraform | `terraform` modules in repo |

## 4.3 Versioning

| Component | Version |
|-----------|---------|
| vCenter Server | 7.0 U3 |
| ESXi | 7.0 U3 |
| NSX‑T Manager | 3.2.1 |
| NSX‑T Edge | 3.2.1 |
| Aria Network Insight | 1.4.0 |
| Aria Automation | 2.3.0 |
| Aria Orchestrator | 2.1.0 |
| Aria Operations | 3.0.0 |
| Aria Logs | 3.0.0 |
| Aria Suite Lifecycle Manager | 1.2.0 |
| Ansible | 2.12 |
| Terraform | 1.5 |

## 4.4 Installation Notes

- All components must be installed in the same vSphere SDDC cluster.  
- NSX‑T must be licensed and activated before deploying Edge services.  
- Aria Network Insight requires a dedicated subnet with sufficient IP addresses.  
- GitHub Actions runners must have network access to the SDDC and Aria services.  

---

# 5. Pre‑Requisites

## 5.1 Infrastructure

- Compute: 4 x ESXi hosts, each with 64 GB RAM, 16 vCPU.  
- Storage: vSAN cluster with 1 TB usable capacity.  
- Network: 3‑tiered network (Management, Control, Data).  
- DNS: Internal DNS for all services.  
- NTP: Synchronized to `ntp.example.com`.  
- Backup Infrastructure: Aria Backup or equivalent.  

## 5.2 Hardware Requirements

| CPU | Memory | Storage | Rack | BIOS |
|-----|--------|---------|------|------|
| 16 vCPU per host | 64 GB | 1 TB | 2U | Default |

## 5.3 Software Requirements

- Operating System: VMware ESXi 7.0 U3  
- Middleware: vCenter Server 7.0 U3  
- Runtime Components: NSX‑T 3.2.1, Aria Suite 2.x  
- Libraries: Python 3.9, Go 1.18  
- Utilities: Ansible 2.12, Terraform 1.5, Git 2.34  

## 5.4 Access & Permissions

| Role | Permissions | Notes |
|------|-------------|-------|
| Platform Engineer | vCenter Admin, NSX‑T Admin, Aria Admin | Full control |
| Operations | Aria Ops Viewer | Read‑only |
| Security | Security Auditor | Read‑only |

## 5.5 Security Requirements

- All management traffic must be encrypted (TLS 1.2+).  
- Secrets stored in HashiCorp Vault.  
- Role‑based access control enforced across all services.  

## 5.6 Secrets & Credential Dependencies

| Credential Type | Purpose | Storage Location |
|-----------------|---------|------------------|
| vCenter Password | vCenter login | HashiCorp Vault |
| NSX‑T Admin | NSX‑T admin | HashiCorp Vault |
| Aria API Key | Aria services | HashiCorp Vault |
| GitHub Token | CI/CD | GitHub Secrets |

## 5.7 Certificate Requirements

| Certificate | Purpose | Owner |
|-------------|---------|-------|
| vCenter SSL | Management | IT Security |
| NSX‑T SSL | Control | IT Security |
| Aria SSL | Data | IT Security |

## 5.8 Firewall & Network
