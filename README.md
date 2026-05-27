# CCNA Enterprise Campus Mega Lab

## Overview
This repository documents a complete CCNA-level enterprise campus lab built in Cisco Packet Tracer. The project integrates switching, routing, first-hop redundancy, wireless, security controls, and core services in one topology across two office blocks. The included Packet Tracer file and sanitized running configurations are the primary implementation artifacts.

## Lab Objectives
- Build a hierarchical campus design with edge, core, distribution, and access layers.
- Segment traffic by VLAN for users, voice, servers, wireless, and management.
- Implement resilient forwarding with EtherChannel, STP tuning, HSRP, and OSPF.
- Provide shared services (DHCP, DNS, NTP, Syslog, SNMP, SSH, NAT/PAT).
- Apply access-layer protections (ACLs, Port Security, DHCP Snooping, DAI).

## Topology
![Packet Tracer topology](assets/screenshots/packet-tracer-topology.png)

A logical Mermaid version is also included at [`diagrams/logical-topology.mmd`](diagrams/logical-topology.mmd).

## Device Roles
| Device | Role |
|---|---|
| R1 | Edge router, DHCP server, NAT/PAT, OSPF ASBR, NTP master |
| CSW1 | Core multilayer switch, routed uplinks, OSPF router ID 10.0.0.77 |
| DSW-A1 | Office A distribution switch, SVIs, HSRP, STP root tuning, OSPF router ID 10.0.0.79 |
| DSW-B1 | Office B distribution switch, SVIs, HSRP, STP root tuning, OSPF router ID 10.0.0.81 |
| ASW-A1 | Office A access switch serving LWAP1/WLC1 access block |
| ASW-B1 | Office B access switch serving LWAP2 access block |
| WLC1 | Wireless LAN controller in Office A |
| SRV1 | Server endpoint in Office B server VLAN |

## VLAN and IP Addressing Plan
### Office A
| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.0/28 | 10.0.0.1 |
| 10 | PCs | 10.1.0.0/24 | 10.1.0.1 |
| 20 | Phones | 10.2.0.0/24 | 10.2.0.1 |
| 40 | Wireless | 10.6.0.0/24 | 10.6.0.1 |

### Office B
| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.16/28 | 10.0.0.17 |
| 10 | PCs | 10.3.0.0/24 | 10.3.0.1 |
| 20 | Phones | 10.4.0.0/24 | 10.4.0.1 |
| 30 | Servers | 10.5.0.0/24 | 10.5.0.1 |

## Implementation
### 1. Basic Device Hardening
**Purpose:** Secure management access and baseline control-plane behavior.  
**What was configured:** Encrypted enable secret, local user authentication, SSHv2, Telnet disabled, VTY ACL, logging, and NTP client settings on switching/routing nodes.  
**Key commands:** `enable secret`, `username ... secret`, `ip ssh version 2`, `transport input ssh`, `access-class 1 in`.  
**Validation:** `show ip ssh`, `show running-config | section line vty`, `show access-lists`.

### 2. VLAN Segmentation
**Purpose:** Separate user, voice, server, wireless, and management traffic.  
**What was configured:** VLANs 10/20/30/40/99 with endpoint-to-VLAN mapping through access ports and VLAN-aware trunks.  
**Validation:** `show vlan brief`.

### 3. Trunking and Native VLAN Design
**Purpose:** Carry required VLANs across access/distribution links while limiting unwanted propagation.  
**What was configured:** 802.1Q trunks, explicit allowed VLAN lists, native VLAN 1000 for standard uplinks, and `nonegotiate`. ASW-A1 F0/2 to WLC1 uses native VLAN 99 with allowed VLANs 40 and 99.  
**Validation:** `show interfaces trunk`.

### 4. EtherChannel
**Purpose:** Increase bandwidth and provide resilient link aggregation.  
**What was configured:**
- DSW-A1 ↔ DSW-A2: Layer 2 EtherChannel, PAgP desirable, Port-channel1
- DSW-B1 ↔ DSW-B2: Layer 2 EtherChannel, LACP active, Port-channel1
- CSW1 ↔ CSW2: Layer 3 EtherChannel, PAgP desirable, Port-channel1, routed /30 between 10.0.0.41 and 10.0.0.42  
**Validation:** `show etherchannel summary`.

### 5. Spanning Tree Protocol
**Purpose:** Prevent loops and align forwarding with active gateways.  
**What was configured:** Rapid PVST+ on the active Layer 2 switching domain/distribution-access layer. DSW-A1 root priority 0 for VLANs 10/99 and 4096 for VLANs 20/40. DSW-B1 root priority 0 for VLANs 10/99 and 4096 for VLANs 20/30.  
**Validation:** `show spanning-tree root`, `show spanning-tree vlan 10,20,30,40,99`.

### 6. Inter-VLAN Routing and HSRP
**Purpose:** Provide default gateway redundancy and inter-VLAN routing.  
**What was configured:** SVI gateways on distribution switches with HSRPv2 role split across VLAN groups.  
**Validation:** `show standby brief`, `show ip interface brief`.

### 7. OSPF Dynamic Routing
**Purpose:** Exchange internal routes across edge/core/distribution routed links.  
**What was configured:** OSPF process 1 in area 0, loopback-based router IDs, passive user VLAN SVIs on distribution switches, point-to-point OSPF network type on routed links, and default route injection from R1.  
**Validation:** `show ip ospf neighbor`, `show ip route ospf`.

### 8. Static and Floating Default Routes
**Purpose:** Keep primary/backup Internet path behavior deterministic.  
**What was configured:** R1 static default route plus higher-AD floating default route for backup ISP path.  
**Validation:** `show ip route`, failover traffic test.

### 9. DHCP and DHCP Relay
**Purpose:** Provide centralized address assignment across campus VLANs.  
**What was configured:** R1 DHCP pools A-Mgmt, A-PC, A-Phone, B-Mgmt, B-PC, B-Phone, and Wi-Fi; helper-address on distribution SVIs.  
**Validation:** `show ip dhcp binding`, `show ip dhcp pool`.

### 10. DNS
**Purpose:** Provide hostname resolution to clients.  
**What was configured:** DNS server reference set to SRV1 (10.5.0.4) in DHCP pools and device management configuration.  
**Validation:** End-host name resolution tests.

### 11. NTP
**Purpose:** Keep network devices time-aligned.  
**What was configured:** R1 `ntp master 5`; campus devices configured as NTP clients with key trust settings.  
**Validation:** `show ntp status`.

### 12. Syslog and SNMP
**Purpose:** Centralize observability and telemetry.  
**What was configured:** Remote logging to SRV1 and SNMP RO community on infrastructure devices.  
**Validation:** `show logging`, `show snmp`.

### 13. FTP and IOS Image Management
**Purpose:** Support image transfer workflow from the router management plane.  
**What was configured:** FTP client credentials on R1 (sanitized in published config).  
**Validation:** `show running-config | include ip ftp`.

### 14. SSH Remote Management
**Purpose:** Encrypt remote administrative access.  
**What was configured:** SSH version 2, Telnet disabled via `transport input ssh`, and VTY ACL 1 permitting 10.1.0.0/24.  
**Validation:** `show ip ssh`, `show access-lists`.

### 15. NAT and PAT
**Purpose:** Provide outbound Internet access and selective inbound publishing.  
**What was configured:** NAT inside on G0/0 and G0/1; NAT outside on G0/0/0 and G0/1/0; static NAT 10.5.0.4→203.0.113.113; PAT pool 203.0.113.200–203.0.113.207/29; ACL 2 permits 10.1.0.0/24, 10.2.0.0/24, 10.3.0.0/24, 10.4.0.0/24, 10.6.0.0/24.  
**Validation:** `show ip nat translations`, `show ip nat statistics`.

### 16. CDP and LLDP
**Purpose:** Limit unnecessary protocol exposure while keeping neighbor visibility.  
**What was configured:** CDP disabled and LLDP enabled on managed infrastructure interfaces where configured.  
**Validation:** `show cdp`, `show lldp neighbors`.

### 17. ACLs
**Purpose:** Control traffic flow between selected segments.  
**What was configured:** Standard ACL for VTY management restriction and extended ACL policy (for example OfficeA_to_OfficeB on distribution).  
**Validation:** `show access-lists`.

### 18. Port Security
**Purpose:** Reduce risk of rogue endpoint attachment on host-facing access ports.  
**What was configured:** Sticky MAC learning with violation restrict.  
**Validation:** `show port-security`, `show port-security address`.

### 19. DHCP Snooping
**Purpose:** Block unauthorized DHCP server behavior.  
**What was configured:** Snooping enabled on user VLANs with trust on uplinks and rate limiting on host ports.  
**Validation:** `show ip dhcp snooping`.

### 20. Dynamic ARP Inspection
**Purpose:** Mitigate ARP spoofing/poisoning.  
**What was configured:** DAI enabled for user VLANs with trusted uplinks aligned to Snooping trust boundaries.  
**Validation:** `show ip arp inspection`.

### 21. IPv6
**Purpose:** Include dual-stack routing in edge/core segments.  
**What was configured:** IPv6 unicast routing, IPv6 interface addressing, default and floating default routes on R1.  
**Validation:** `show ipv6 interface brief`, `show ipv6 route`.

### 22. Wireless LAN Controller
**Purpose:** Provide centralized AP control and WLAN delivery.  
**What was configured:** WLC1 connected to ASW-A1, LWAP1 on ASW-A1, LWAP2 on ASW-B1, AP discovery via DHCP option 43 to 10.0.0.7, wireless user VLAN 40 mapping.  
**Validation:** WLC AP/client summary and CAPWAP join checks.

## Security Controls Summary
| Control | Implementation |
|---|---|
| SSH-only remote management | `ip ssh version 2`, `transport input ssh` |
| VTY ACL | ACL 1 permits 10.1.0.0/24 |
| SNMP read-only | `snmp-server community <REDACTED_SNMP_STRING> RO` |
| Port Security | Sticky MAC + restrict on host ports |
| DHCP Snooping | Enabled with trusted uplinks |
| Dynamic ARP Inspection | Enabled on user VLANs |
| BPDU Guard + PortFast | Host-facing protection on access ports |
| CDP/LLDP posture | CDP disabled and LLDP enabled where configured |

## Verification Commands Used / Validation Checklist
See [`verification/validation-checklist.md`](verification/validation-checklist.md).

## Troubleshooting Notes Summary
Deeper troubleshooting runbook is documented in [`docs/troubleshooting-notes.md`](docs/troubleshooting-notes.md). It covers OSPF adjacency failures, HSRP convergence issues, DHCP relay gaps, trunk/EtherChannel mismatches, port-security lockouts, DAI/Snooping behavior, NAT issues, and WLC/AP registration checks.

## Repository Structure
```text
CCNA-Enterprise-Campus-Mega-Lab/
├── README.md
├── LICENSE
├── .gitignore
├── packet-tracer/
│   ├── ccna-enterprise-campus-mega-lab.pkt
│   └── README.md
├── configs/
│   ├── routers/R1-running-config.txt
│   ├── core-switches/CSW1-running-config.txt
│   ├── distribution-switches/DSW-A1-running-config.txt
│   ├── distribution-switches/DSW-B1-running-config.txt
│   └── access-switches/
│       ├── ASW-A1-running-config.txt
│       └── ASW-B1-running-config.txt
├── diagrams/
│   ├── logical-topology.mmd
│   ├── physical-connections.md
│   ├── vlan-ip-plan.md
│   ├── ospf-routing-design.md
│   ├── hsrp-stp-alignment.md
│   └── wireless-wlc-capwap-flow.md
├── docs/
│   ├── implementation-details.md
│   ├── troubleshooting-notes.md
│   └── lessons-learned.md
├── verification/
│   └── validation-checklist.md
└── assets/screenshots/
    └── packet-tracer-topology.png
```

## How to Review the Repo
1. Open `packet-tracer/ccna-enterprise-campus-mega-lab.pkt` to inspect the live topology and device state.
2. Review architecture and implementation flow in this README.
3. Cross-check detailed behavior in `docs/implementation-details.md`.
4. Validate command coverage using `verification/validation-checklist.md`.
5. Compare specific implementation claims against the six published running configs under `configs/`.
