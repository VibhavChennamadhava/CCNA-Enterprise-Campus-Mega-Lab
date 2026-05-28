# CCNA Enterprise Campus Mega Lab

> A full-scale Cisco Packet Tracer enterprise campus network that combines switching, routing, services, security, wireless, and IPv6 into one integrated CCNA implementation.

---

## Table of Contents
- [Overview](#overview)
- [Lab Objectives](#lab-objectives)
- [Topology](#topology)
- [Device Roles](#device-roles)
- [VLAN and IP Addressing Design](#vlan-and-ip-addressing-design)
- [Configurations / Implementation](#configurations--implementation)
  - [1. Basic Device Hardening](#1-basic-device-hardening)
  - [2. VLAN Segmentation](#2-vlan-segmentation)
  - [3. Trunking and Native VLAN Design](#3-trunking-and-native-vlan-design)
  - [4. EtherChannel](#4-etherchannel)
  - [5. Spanning Tree Protocol](#5-spanning-tree-protocol)
  - [6. Inter-VLAN Routing and HSRP](#6-inter-vlan-routing-and-hsrp)
  - [7. OSPF Dynamic Routing](#7-ospf-dynamic-routing)
  - [8. Static and Floating Default Routes](#8-static-and-floating-default-routes)
  - [9. DHCP and DHCP Relay](#9-dhcp-and-dhcp-relay)
  - [10. DNS](#10-dns)
  - [11. NTP](#11-ntp)
  - [12. Syslog and SNMP](#12-syslog-and-snmp)
  - [13. FTP and IOS Image Management](#13-ftp-and-ios-image-management)
  - [14. SSH Remote Management](#14-ssh-remote-management)
  - [15. NAT and PAT](#15-nat-and-pat)
  - [16. CDP and LLDP](#16-cdp-and-lldp)
  - [17. ACLs](#17-acls)
  - [18. Port Security](#18-port-security)
  - [19. DHCP Snooping](#19-dhcp-snooping)
  - [20. Dynamic ARP Inspection](#20-dynamic-arp-inspection)
  - [21. IPv6](#21-ipv6)
  - [22. Wireless LAN Controller](#22-wireless-lan-controller)
- [Security Controls Summary](#security-controls-summary)
- [Operational Validation Commands](#operational-validation-commands)
- [Troubleshooting Notes](#troubleshooting-notes)
- [Repository Structure](#repository-structure)
- [How to Review This Lab](#how-to-review-this-lab)

---

## Overview

This repository documents a complete CCNA Enterprise Campus Mega Lab built in Cisco Packet Tracer. The design uses a hierarchical model with an edge router, dual core switches, dual distribution blocks, and dual-homed access switching across two office segments.

The lab integrates Layer 2 switching, first-hop redundancy, dynamic routing, centralized services, security controls, wireless AP/WLC operation, and IPv6 routing. The main proof artifact is the included Packet Tracer file:

- `packet-tracer/ccna-enterprise-campus-mega-lab.pkt`

---

## Lab Objectives

- Build a hierarchical enterprise campus with edge, core, distribution, and access layers.
- Segment traffic by VLAN for management, PCs, phones, wireless, and servers.
- Implement resilient L2/L3 forwarding with EtherChannel, STP tuning, HSRP, and OSPF.
- Provide centralized services: DHCP, DNS, NTP, Syslog, SNMP, SSH, and NAT/PAT.
- Apply access/security controls: ACLs, Port Security, DHCP Snooping, and Dynamic ARP Inspection.
- Validate dual-stack operation with IPv6 routing and default route resiliency.

---

## Topology

![Packet Tracer topology](assets/screenshots/packet-tracer-topology.png)

Logical diagram source:
- `diagrams/logical-topology.mmd`

Primary lab artifact:
- `packet-tracer/ccna-enterprise-campus-mega-lab.pkt`

---

## Device Roles

| Device | Role |
|---|---|
| R1 | Edge router, OSPF ASBR, DHCP server, NAT/PAT gateway, NTP master |
| CSW1 | Core multilayer switch, routed transit, OSPF backbone participation |
| DSW-A1 | Office A distribution switch, SVIs, HSRP, STP root role, OSPF |
| DSW-B1 | Office B distribution switch, SVIs, HSRP, STP root role, OSPF |
| ASW-A1 | Office A access switch for LWAP1 and WLC1 integration |
| ASW-B1 | Office B access switch for LWAP2 integration |
| WLC1 | Centralized wireless LAN controller located in Office A |
| SRV1 | Server endpoint in VLAN 30 (Office B server segment) |

---

## VLAN and IP Addressing Design

### Office A

| VLAN ID | Purpose | Subnet | HSRP Virtual IP |
|---|---|---|---|
| 99 | Management | 10.0.0.0/28 | 10.0.0.1 |
| 10 | PCs | 10.1.0.0/24 | 10.1.0.1 |
| 20 | Phones | 10.2.0.0/24 | 10.2.0.1 |
| 40 | Wireless | 10.6.0.0/24 | 10.6.0.1 |

### Office B

| VLAN ID | Purpose | Subnet | HSRP Virtual IP |
|---|---|---|---|
| 99 | Management | 10.0.0.16/28 | 10.0.0.17 |
| 10 | PCs | 10.3.0.0/24 | 10.3.0.1 |
| 20 | Phones | 10.4.0.0/24 | 10.4.0.1 |
| 30 | Servers | 10.5.0.0/24 | 10.5.0.1 |

---

## Configurations / Implementation

### 1. Basic Device Hardening
**Purpose:** Establish secure baseline management access.  
**What was configured:** Local authentication, encrypted secrets, SSHv2, VTY restrictions, and logging settings.  
**Key commands:** `enable secret`, `username ... secret`, `ip ssh version 2`, `transport input ssh`, `access-class 1 in`.

### 2. VLAN Segmentation
**Purpose:** Separate management, user, voice, server, and wireless traffic.  
**What was configured:** VLANs 10/20/30/40/99 with role-based subnet separation and gateway SVIs at distribution.

### 3. Trunking and Native VLAN Design
**Purpose:** Carry required VLANs across inter-switch links while controlling propagation.  
**What was configured:** 802.1Q trunks, explicit allowed VLAN lists, `switchport nonegotiate`, and native VLAN controls.

### 4. EtherChannel
**Purpose:** Add bandwidth and L2/L3 link resilience.  
**What was configured:**
- DSW-A1 ↔ DSW-A2: Layer 2 EtherChannel using PAgP desirable
- DSW-B1 ↔ DSW-B2: Layer 2 EtherChannel using LACP active
- CSW1 ↔ CSW2: Layer 3 EtherChannel using PAgP desirable (not static `mode on`)

### 5. Spanning Tree Protocol
**Purpose:** Prevent loops and align forwarding paths with gateway placement.  
**What was configured:** Rapid PVST+ in the distribution/access Layer 2 domain where applicable, with root placement aligned to HSRP design:
- Office A VLANs 10 and 99: DSW-A1 root
- Office A VLANs 20 and 40: DSW-A2 root
- Office B VLANs 10 and 99: DSW-B1 root
- Office B VLANs 20 and 30: DSW-B2 root

### 6. Inter-VLAN Routing and HSRP
**Purpose:** Provide resilient default gateways and L3 inter-VLAN forwarding.  
**What was configured:** HSRPv2 on distribution SVIs with active/standby role balancing across VLAN groups.

### 7. OSPF Dynamic Routing
**Purpose:** Exchange routes across edge/core/distribution routed links.  
**What was configured:** OSPF process 1 in area 0, loopback router IDs, passive interfaces on loopbacks and user VLAN SVIs where configured, point-to-point OSPF network type on routed links, and `default-information originate` on R1.

### 8. Static and Floating Default Routes
**Purpose:** Support primary and backup Internet exit behavior.  
**What was configured:** Primary static default route and higher administrative distance floating default route on R1.

### 9. DHCP and DHCP Relay
**Purpose:** Centralized DHCP service delivery for campus VLANs.  
**What was configured:** R1 DHCP pools for:
- Office A: Management, PCs, Phones
- Office B: Management, PCs, Phones
- Wireless clients: VLAN 40

SRV1 is a static server endpoint in VLAN 30.

### 10. DNS
**Purpose:** Provide name resolution for clients and managed devices.  
**What was configured:** DNS server reference set to SRV1 (10.5.0.4) in DHCP and management settings.

### 11. NTP
**Purpose:** Keep infrastructure clocks synchronized.  
**What was configured:** R1 is configured as the NTP master using `ntp master 5`, with campus devices configured as NTP clients.

### 12. Syslog and SNMP
**Purpose:** Centralized monitoring and event logging.  
**What was configured:** Syslog forwarding to SRV1 and SNMP read-only community on network devices.

### 13. FTP and IOS Image Management
**Purpose:** Support IOS transfer/backup workflow from the edge device.  
**What was configured:** FTP client credentials on R1 (sanitized in published config).

### 14. SSH Remote Management
**Purpose:** Secure remote administration.  
**What was configured:** SSH version 2 enforced, Telnet disabled using `transport input ssh`, VTY access restricted using ACL 1, and ACL 1 permits `10.1.0.0/24`.

### 15. NAT and PAT
**Purpose:** Provide outbound Internet connectivity and selective inbound reachability.  
**What was configured:**
- NAT inside: R1 G0/0 and G0/1 toward the campus/core
- NAT outside: R1 G0/0/0 and G0/1/0 toward ISP/Internet
- PAT pool: 203.0.113.200–203.0.113.207 /29
- Static NAT: 10.5.0.4 mapped to 203.0.113.113
- ACL 2 permits 10.1.0.0/24, 10.2.0.0/24, 10.3.0.0/24, 10.4.0.0/24, and 10.6.0.0/24

### 16. CDP and LLDP
**Purpose:** Control neighbor discovery exposure while retaining standards-based visibility.  
**What was configured:** CDP disabled and LLDP enabled where configured.

### 17. ACLs
**Purpose:** Restrict management and inter-segment traffic flows.  
**What was configured:** Standard ACL for VTY management restrictions and extended ACL policy controls at distribution.

### 18. Port Security
**Purpose:** Reduce unauthorized endpoint attachment risk.  
**What was configured:** Sticky MAC learning with violation restrict on host-facing access ports.

### 19. DHCP Snooping
**Purpose:** Protect against rogue DHCP behavior.  
**What was configured:** DHCP Snooping enabled with trusted uplinks and host-port controls.

### 20. Dynamic ARP Inspection
**Purpose:** Mitigate ARP spoofing on user VLANs.  
**What was configured:** DAI enabled with trust boundaries aligned to Snooping trusted uplinks.

### 21. IPv6
**Purpose:** Validate dual-stack routing behavior.  
**What was configured:** IPv6 unicast routing, IPv6 interface addressing, and primary/backup IPv6 default routes.

### 22. Wireless LAN Controller
**Purpose:** Centralize AP management and WLAN service policy.  
**What was configured:**
- WLC1 is in Office A and connects through ASW-A1
- LWAP1 connects through ASW-A1
- LWAP2 connects through ASW-B1
- APs discover WLC1 through DHCP option 43
- Wireless client VLAN is VLAN 40
- WLC1 provides centralized AP/WLAN control

---

## Security Controls Summary

| Control | Implementation |
|---|---|
| SSH-only remote access | `ip ssh version 2` and `transport input ssh` |
| VTY source restriction | ACL 1 permits `10.1.0.0/24` |
| SNMP least privilege | Read-only SNMP community |
| Port Security | Sticky MAC + restrict violation mode |
| DHCP Snooping | Enabled with trusted uplinks |
| Dynamic ARP Inspection | Enabled on user VLANs |
| STP edge protection | PortFast and BPDU Guard on host-facing ports |
| L2 discovery posture | CDP disabled and LLDP enabled where configured |

---

## Operational Validation Commands

```bash
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree root
show standby brief
show ip ospf neighbor
show ip route ospf
show ip dhcp binding
show ip nat translations
show access-lists
show port-security
show ip dhcp snooping
show ip arp inspection
show ipv6 route
show ip ssh
```

---

## Troubleshooting Notes

- **OSPF neighbor not forming:** Verify area, addressing, passive-interface usage, and point-to-point network type.
- **HSRP not converging:** Verify SVI status, group/VIP parity, and priority/preempt behavior.
- **DHCP clients not receiving addresses:** Verify helper addresses, trunk VLAN allowance, and DHCP pool subnet/default-router values.
- **Trunk VLAN issues:** Verify native VLAN consistency and allowed VLAN lists on both ends.
- **EtherChannel mismatch:** Verify protocol/mode parity (PAgP/LACP), member consistency, and trunk settings.
- **Port Security sticky MAC issues:** Verify violation mode and clear stale sticky entries as needed.
- **DAI/DHCP Snooping behavior:** Verify trusted uplinks and VLAN scope.
- **NAT translations not appearing:** Verify inside/outside roles, ACL 2 traffic match, and active flow generation.
- **WLC/AP registration issues:** Verify AP management VLAN reachability, DHCP option 43, and CAPWAP control path.

---

## Repository Structure

```text
CCNA-Enterprise-Campus-Mega-Lab/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── packet-tracer/
│   ├── ccna-enterprise-campus-mega-lab.pkt
│   └── README.md
│
├── configs/
│   ├── routers/
│   │   └── R1-running-config.txt
│   ├── core-switches/
│   │   └── CSW1-running-config.txt
│   ├── distribution-switches/
│   │   ├── DSW-A1-running-config.txt
│   │   └── DSW-B1-running-config.txt
│   └── access-switches/
│       ├── ASW-A1-running-config.txt
│       └── ASW-B1-running-config.txt
│
├── diagrams/
│   ├── logical-topology.mmd
│   ├── vlan-ip-plan.md
│   ├── ospf-routing-design.md
│   ├── hsrp-stp-alignment.md
│   └── wireless-wlc-capwap-flow.md
│
└── assets/
    └── screenshots/
        └── packet-tracer-topology.png
```

---

## How to Review This Lab

1. Open `packet-tracer/ccna-enterprise-campus-mega-lab.pkt` to inspect the full configured topology.
2. Read this README top-to-bottom for end-to-end design and implementation details.
3. Cross-check implementation details against the six sanitized running-config files under `configs/`.
4. Use the operational validation commands above to verify control-plane and service behavior in Packet Tracer.
5. Refer to diagram sources under `diagrams/` for logical architecture and protocol design mapping.
