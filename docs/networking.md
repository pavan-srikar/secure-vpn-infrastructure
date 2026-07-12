# Networking Overview

The VPN infrastructure uses WireGuard to establish secure encrypted tunnels between remote clients and an AWS-hosted VPN gateway. The networking architecture supports multiple routing modes, allowing the VPN to be used for personal, enterprise, or hybrid networking scenarios.

---

# Network Architecture

```
                Internet
                    │
        ┌───────────┴───────────┐
        │                       │
   Remote Laptop          Mobile Device
        │                       │
        └──── Encrypted WireGuard Tunnel ────┐
                                             │
                                   AWS EC2 VPN Gateway
                                             │
                          ┌──────────────────┴─────────────────┐
                          │                                    │
                  Internal VPN Network                 Internet Access
                  (10.0.0.0/24)                  (Optional Full Tunnel)
```

---

# VPN Address Space

The VPN uses a dedicated private subnet.

```
10.0.0.0/24
```

Example assignments:

| Device | VPN IP |
|---------|---------|
| VPN Gateway | 10.0.0.1 |
| Laptop | 10.0.0.2 |
| Mobile | 10.0.0.3 |
| Additional Peer | 10.0.0.x |

Each peer receives a unique private address during provisioning.

---

# Routing Modes

The infrastructure supports multiple routing configurations.

## Split Tunnel

Only selected private networks are routed through the VPN.

Example:

```
AllowedIPs = 10.0.0.0/24
```

Suitable for:

- Secure access to internal infrastructure
- Development environments
- Reduced bandwidth usage
- Enterprise remote access

---

## Full Tunnel

All client traffic is routed through the VPN gateway.

Example:

```
AllowedIPs = 0.0.0.0/0
```

Benefits:

- Secure browsing on public networks
- Centralized outbound traffic
- Public IP masking

---

## Enterprise Mode

Enterprise mode combines secure internal access with configurable routing policies.

Typical use cases include:

- Internal servers
- Private APIs
- Bastion access
- Administrative workloads

Routing policies can be customized based on organizational requirements.

---

# IP Forwarding

Linux IP forwarding is enabled on the VPN gateway, allowing traffic to be forwarded between VPN clients and external networks.

```
net.ipv4.ip_forward = 1
```

---

# Network Address Translation (NAT)

iptables performs NAT masquerading to allow VPN clients to access external networks through the EC2 instance.

This provides:

- Internet access
- Address translation
- Simplified routing
- Secure outbound connectivity

---

# Peer Connectivity

Each client connects independently using its own:

- Public key
- Private key
- Assigned VPN IP
- Allowed IP routes

Peers remain isolated unless routing policies explicitly permit communication.

---

# High-Level Network Flow

1. Client establishes an encrypted WireGuard tunnel.
2. WireGuard authenticates the peer.
3. Traffic is routed according to the selected routing mode.
4. iptables performs NAT when Internet access is required.
5. Responses return through the encrypted tunnel.

---

# Design Goals

- Secure remote access
- Low-latency encrypted networking
- Flexible routing policies
- Enterprise-style VPN architecture
- Infrastructure automation compatibility