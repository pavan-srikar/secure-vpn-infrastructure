# Architecture

## Overview

This project implements a production-inspired WireGuard VPN infrastructure on AWS using Infrastructure as Code, automated provisioning, and modern observability tooling.

The architecture focuses on secure remote access, reproducible deployments, operational visibility, and enterprise networking practices.

---

# High-Level Architecture

```
                         Internet
                             │
                ┌────────────┴────────────┐
                │                         │
         Remote Laptop             Mobile Device
                │                         │
                └────── WireGuard VPN ────┘
                            │
                            ▼
                 AWS EC2 VPN Gateway
                 Ubuntu + WireGuard
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
          ▼                 ▼                 ▼
     Prometheus      Node Exporter   WireGuard Exporter
          │
          ▼
       Grafana
          │
          ▼
 Real-Time Monitoring Dashboard
```

---

# Components

## AWS Infrastructure

Infrastructure is provisioned entirely using Terraform.

Resources include:

- EC2 Instance
- VPC
- Public Subnet
- Internet Gateway
- Route Tables
- Security Groups
- Elastic IP (optional)
- SSH Allowlist Validation

The infrastructure can be recreated consistently using Infrastructure as Code.

---

## WireGuard VPN

WireGuard provides encrypted VPN tunnels between remote clients and the cloud gateway.

Features include:

- Secure encrypted tunnels
- Lightweight protocol
- Public/private key authentication
- Low latency
- High performance

Each client receives:

- Unique VPN IP
- Private key
- Public key
- Client configuration
- QR code for mobile onboarding

---

## Automation

Deployment automation is implemented using Bash scripts.

Automation includes:

- WireGuard installation
- Server configuration
- Peer creation
- Client configuration generation
- QR code generation
- Routing configuration
- Firewall setup

This minimizes manual configuration and ensures repeatable deployments.

---

## Routing

The VPN supports multiple networking modes.

### Split Tunnel

Only internal traffic passes through the VPN.

Example:

```
AllowedIPs = 10.0.0.0/24
```

---

### Full Tunnel

All client traffic is routed through the VPN gateway.

Example:

```
AllowedIPs = 0.0.0.0/0
```

---

### Enterprise Mode

Supports configurable routing policies for private infrastructure and administrative access while maintaining secure connectivity.

---

## Monitoring & Observability

The monitoring stack provides visibility into both infrastructure and VPN health.

### Prometheus

Collects infrastructure and VPN metrics.

### Node Exporter

Provides host-level metrics including:

- CPU
- Memory
- Disk
- Network
- Load Average

### WireGuard Metrics Exporter

Exports VPN-specific metrics including:

- Connected peers
- Latest handshake
- Bytes transmitted
- Bytes received
- Peer status

### Grafana

Visualizes collected metrics using dashboards.

Dashboards provide insight into:

- VPN health
- Connected clients
- Resource utilization
- Network throughput
- System performance

---

# Security Architecture

Security controls include:

- WireGuard encryption
- Least-privilege Security Groups
- SSH allowlist enforcement
- Terraform validation
- Private VPN subnet
- Infrastructure as Code
- Automated provisioning

Administrative access is restricted while VPN communication remains encrypted end-to-end.

---

# Deployment Workflow

```
Terraform Apply
        │
        ▼
Provision AWS Infrastructure
        │
        ▼
Configure EC2 Instance
        │
        ▼
Install WireGuard
        │
        ▼
Configure Firewall & Routing
        │
        ▼
Create VPN Peers
        │
        ▼
Generate Client Configurations
        │
        ▼
Generate QR Codes
        │
        ▼
Deploy Monitoring Stack
        │
        ▼
Infrastructure Ready
```

---

# Design Goals

The project was designed around the following objectives:

- Secure remote connectivity
- Infrastructure as Code
- Automated provisioning
- Reproducible deployments
- Operational observability
- Enterprise networking practices
- Cloud-native infrastructure management

---

# Technology Stack

| Layer | Technologies |
|--------|--------------|
| Cloud | AWS EC2, VPC |
| VPN | WireGuard |
| Infrastructure as Code | Terraform |
| Automation | Bash |
| Monitoring | Prometheus |
| Visualization | Grafana |
| System Metrics | Node Exporter |
| VPN Metrics | WireGuard Metrics Exporter |
| Operating System | Ubuntu Linux |

---

# Future Evolution

Potential future enhancements include:

- High Availability (Multi-AZ)
- Kubernetes deployment
- CI/CD automation
- Identity provider integration (Azure AD / LDAP)
- Centralized peer management
- Alertmanager integration
- Multi-region deployments
- Role-Based Access Control (RBAC)