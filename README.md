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

This repository documents a complete enterprise-style campus network built in Cisco Packet Tracer as part of **Jeremy's IT Lab CCNA Mega Lab**. The topology simulates a real-world multi-site environment with a hierarchical three-layer design (core, distribution, access), redundant L2/L3 paths, dynamic routing, centralized network services, layered access-layer security, wireless LAN integration, IPv6, and network management.

Rather than treating each topic as an isolated exercise, this lab connects every CCNA technology domain into a single working network, requiring each piece to interoperate correctly before the next layer can function.

---

## Lab Objectives

- Design a hierarchical campus topology with core, distribution, and access layers
- Segment traffic using VLANs for users, voice, servers, management, and wireless
- Aggregate and redundantize L2 links using EtherChannel (PAgP and LACP)
- Optimize STP by aligning root bridge placement with HSRP active gateways
- Provide first-hop redundancy for all user VLANs using HSRPv2
- Implement OSPF as the internal dynamic routing protocol
- Centralize DHCP, DNS, NTP, Syslog, SNMP, FTP, and SSH services on a single server and edge router
- Configure NAT/PAT for Internet access and static NAT for server publishing
- Enforce access-layer security using port security, DHCP snooping, and DAI
- Control inter-office traffic using extended named ACLs
- Configure dual-stack IPv6 routing on edge links
- Deploy centralized wireless services using a Cisco WLC

---

## Topology

```
                        Internet / ISP
                              |
                             [R1]   ← Edge router, DHCP, NAT, OSPF ASBR, NTP master
                              |
                   ┌──────────┴──────────┐
                [CSW1]              [CSW2]          ← Core Layer (L3 EtherChannel)
                   |                    |
        ┌──────────┴──┐          ┌──────┴──────────┐
    [DSW-A1]      [DSW-A2]   [DSW-B1]          [DSW-B2]   ← Distribution Layer
         |              |          |                  |
    [ASW-A1]       [ASW-A2]   [ASW-B1]  [ASW-B2]  [ASW-B3]  ← Access Layer
       |               |          |         |          |
     LWAP           PCs/Phones   LWAP    PCs/Phones  SRV1
       |
     [WLC1]
```

The network is divided into two logical offices:

| Office   | Distribution Switches | Access Switches              |
|----------|-----------------------|------------------------------|
| Office A | DSW-A1, DSW-A2        | ASW-A1, ASW-A2, ASW-A3       |
| Office B | DSW-B1, DSW-B2        | ASW-B1, ASW-B2, ASW-B3       |

Each office has independent user VLANs, voice VLANs, a management subnet, and redundant distribution switches providing gateway failover.

---

## Device Roles

| Device                        | Role                                                                                     |
|-------------------------------|------------------------------------------------------------------------------------------|
| R1                            | Edge router · Internet gateway · DHCP server · NAT · OSPF ASBR · NTP master · FTP client |
| CSW1 / CSW2                   | Core multilayer switches · L3 routing · OSPF backbone · L3 EtherChannel inter-core link  |
| DSW-A1 / DSW-A2               | Office A distribution · SVIs · HSRPv2 · OSPF · DHCP relay · STP root                    |
| DSW-B1 / DSW-B2               | Office B distribution · SVIs · HSRPv2 · OSPF · DHCP relay · STP root                    |
| ASW-A1 / ASW-B1               | Access switches for lightweight access points                                             |
| ASW-A2 / ASW-A3 / ASW-B2      | Access switches for PCs and IP phones                                                    |
| ASW-B3                        | Access switch for server connectivity                                                     |
| WLC1                          | Wireless LAN Controller · centralized AP management · WLAN provisioning                  |
| SRV1                          | Internal server · DNS · Syslog · SNMP · FTP                                              |
| PCs / IP Phones / Laptops     | End-user clients for testing and validation                                              |

---

## VLAN and IP Addressing Plan
### Office A

| VLAN ID | Purpose    | Subnet        | HSRP Virtual IP |
|---------|------------|---------------|-----------------|
| 99      | Management | 10.0.0.0/28   | 10.0.0.1        |
| 10      | PCs        | 10.1.0.0/24   | 10.1.0.1        |
| 20      | Phones     | 10.2.0.0/24   | 10.2.0.1        |
| 40      | Wireless   | 10.6.0.0/24   | 10.6.0.1        |

### Office B

| VLAN ID | Purpose    | Subnet        | HSRP Virtual IP |
|---------|------------|---------------|-----------------|
| 99      | Management | 10.0.0.16/28  | 10.0.0.17       |
| 10      | PCs        | 10.3.0.0/24   | 10.3.0.1        |
| 20      | Phones     | 10.4.0.0/24   | 10.4.0.1        |
| 30      | Servers    | 10.5.0.0/24   | 10.5.0.1        |

---

## Configurations

---

### 1. Basic Device Hardening

**Purpose:** Establish secure, consistent management access on every network device before any services are deployed.

All routers and switches were hardened with encrypted enable secrets, local user accounts, console and VTY login using local authentication, SSH-only remote access, exec timeouts, and synchronous logging to prevent CLI interruption from syslog messages.

**Key configuration elements:**

```
enable secret <secret>
username <user> secret <password>
line console 0
 login local
 exec-timeout 5 0
 logging synchronous
line vty 0 15
 login local
 transport input ssh
 access-class <mgmt-acl> in
```

---

### 2. VLAN Segmentation

**Purpose:** Reduce broadcast domains, logically separate traffic types, and prepare the Layer 2 foundation for routing and security policies.

Separate VLANs were created for PCs, voice, servers, management, and wireless clients. Access ports were assigned to their respective VLANs, and voice VLANs were applied to ports connected to IP phones. Management SVIs were configured on access switches.

| VLAN ID | Traffic Type |
|---------|--------------|
| 10      | PCs          |
| 20      | Phones       |
| 30      | Servers      |
| 40      | Wireless     |
| 99      | Management   |

---

### 3. Trunking and Native VLAN Design

**Purpose:** Carry multiple VLANs across switch-to-switch links while limiting unnecessary VLAN propagation and eliminating DTP auto-negotiation.

All inter-switch links were configured as 802.1Q trunks with explicit allowed VLAN lists. An unused VLAN (1000) was assigned as the native VLAN. DTP was disabled with `nonegotiate` on all trunk ports.

```
switchport mode trunk
switchport trunk allowed vlan <vlan-list>
switchport trunk native vlan 1000
switchport nonegotiate
```

---

### 4. EtherChannel

**Purpose:** Bundle parallel physical links into one logical channel to increase bandwidth and provide link redundancy without STP blocking individual members.

| Location             | Protocol | Mode                |
|----------------------|----------|---------------------|
| Office A (Dist-Core) | PAgP     | desirable           |
| Office B (Dist-Core) | LACP     | active              |
| Core-to-Core link    | Static   | L3 routed port-channel |

**Key verification:**

```
show etherchannel summary
show interfaces port-channel <id>
show interfaces trunk
```

---

### 5. Spanning Tree Protocol

**Purpose:** Prevent Layer 2 loops while optimizing forwarding paths by aligning STP root bridge elections with HSRP active gateway placement.

Rapid PVST+ was configured across all switches. STP root bridge priority was manually tuned so the primary root for each VLAN group matches the HSRP active gateway for that group. PortFast and BPDU Guard were applied on all host-facing access ports.

| VLAN Group          | HSRP Active | STP Root |
|---------------------|-------------|----------|
| Office A — 10, 99   | DSW-A1      | DSW-A1   |
| Office A — 20, 40   | DSW-A2      | DSW-A2   |
| Office B — 10, 99   | DSW-B1      | DSW-B1   |
| Office B — 20, 30   | DSW-B2      | DSW-B2   |

> Aligning the STP root with the HSRP active gateway prevents traffic from being forwarded through a non-optimal distribution switch, which would otherwise add a needless L2 hop on the way to the default gateway.

---

### 6. Inter-VLAN Routing and HSRP

**Purpose:** Route traffic between VLANs at Layer 3 and provide redundant default gateways so end hosts maintain connectivity if one distribution switch fails.

SVIs were configured on distribution switches for each user VLAN. HSRPv2 virtual IPs were assigned as default gateways. Priority and preempt were tuned so gateway roles are predictable and automatically restored after a failure.

**HSRP design example:**

```
Office A — VLAN 10
  DSW-A1: Active  (priority 110, preempt)
  DSW-A2: Standby (priority 100)
  Virtual IP: 10.1.0.1

Office A — VLAN 20
  DSW-A2: Active  (priority 110, preempt)
  DSW-A1: Standby (priority 100)
  Virtual IP: 10.2.0.1
```

**Key verification:**

```
show standby brief
show ip interface brief
```

---

### 7. OSPF Dynamic Routing

**Purpose:** Enable all L3 devices to dynamically learn routes across the network without maintaining static route tables, and inject a default route from R1 so internal devices can reach the Internet.

OSPF process 1, Area 0 was configured on R1, both core switches, and all distribution switches. Loopback interfaces were used for stable router IDs. User VLAN SVIs and loopbacks were set as passive interfaces. Routed point-to-point links used `ip ospf network point-to-point` to avoid DR/BDR elections.

**Design decisions:**

| Decision                         | Reason                                                  |
|----------------------------------|---------------------------------------------------------|
| Loopbacks as router IDs          | Prevents router ID changes on interface flap            |
| Passive on user VLAN SVIs        | Advertises subnets without forming neighbor adjacencies |
| Point-to-point on routed links   | Eliminates unnecessary DR/BDR election overhead         |
| Default route via `default-information originate` | Propagates Internet reachability into OSPF  |

**Key verification:**

```
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
show ip protocols
```

---

### 8. Static and Floating Default Routes

**Purpose:** Provide primary and backup Internet connectivity on R1 using administrative distance to control path preference.

A primary static default route was configured with a standard administrative distance. A floating static route was added with AD 2, making it inactive unless the primary route is withdrawn.

```
ip route 0.0.0.0 0.0.0.0 <primary-next-hop>       ! AD 1 — active
ip route 0.0.0.0 0.0.0.0 <backup-next-hop> 2       ! AD 2 — floating backup
```

---

### 9. DHCP and DHCP Relay

**Purpose:** Provide centralized IP address assignment for all VLANs from a single DHCP server (R1), using relay agents on distribution switch SVIs to forward client broadcasts across VLAN boundaries.

DHCP pools were configured on R1 for every user subnet, including excluded address ranges for network infrastructure. `ip helper-address` was configured on each SVI pointing to R1. DHCP option 43 was included in the wireless pool to enable automatic WLC discovery by lightweight APs.

**Configured pools:**

- Office A: Management, PCs, Phones
- Office B: Management, PCs, Phones, Servers
- Wireless clients (VLAN 40)

**Key verification:**

```
show ip dhcp pool
show ip dhcp binding
show ip dhcp snooping binding
```

---

### 10. DNS

**Purpose:** Allow hosts and network devices to resolve hostnames to IP addresses, and test connectivity using domain names rather than raw IPs.

SRV1 was configured as the internal DNS server with A records and a CNAME alias. All network devices were pointed to SRV1 as their DNS resolver and given a domain name for FQDN resolution.

```
show hosts
ping <domain-name>
```

---

### 11. NTP

**Purpose:** Synchronize clocks across all network devices to ensure consistent timestamps in logs, authentication events, and protocol operations.

R1 was configured as the NTP master (stratum 3) and references an external NTP server. All switches were pointed to R1 as their NTP client. NTP authentication was enabled with a trusted key to prevent rogue time sources.

**Key verification:**

```
show ntp associations
show ntp status
show clock detail
```

---

### 12. Syslog and SNMP

**Purpose:** Enable centralized event logging (Syslog) and remote device polling for status and performance data (SNMP).

All devices were configured to forward log messages to SRV1 at informational trap level. A local logging buffer was also enabled. SNMP read-only community strings were configured to allow SRV1 to poll device information.

```
show logging
show running-config | include snmp
```

---

### 13. FTP and IOS Image Management

**Purpose:** Demonstrate Cisco IOS software lifecycle management: copying images from a central server, setting the boot image, and cleaning up old images from flash.

FTP credentials were configured on R1. An IOS image was copied from SRV1 to flash, the `boot system` command was set to point to the new image, and the old image was deleted after verification.

```
show flash:
show version
show boot
```

---

### 14. SSH Remote Management

**Purpose:** Replace Telnet with encrypted SSH access on all devices and restrict which source subnets can reach VTY lines.

RSA keys (1024-bit) were generated, SSH version 2 was enforced, VTY lines were locked to `transport input ssh`, and a named ACL was applied inbound on VTY to limit SSH access to the Office A management subnet only.

```
show ip ssh
show users
show running-config | section line vty
```

---

### 15. NAT and PAT

**Purpose:** Allow all internal private-addressed hosts to access external networks, and publish SRV1 to the Internet using a static NAT mapping.

| NAT Type    | Configuration                                           |
|-------------|---------------------------------------------------------|
| Static NAT  | 1:1 mapping of SRV1 private IP to a public address      |
| Dynamic PAT | Overloads a public IP pool for all internal user VLANs  |

NAT inside interfaces were assigned on LAN-facing links; the NAT outside interface was assigned on R1's WAN link. An ACL identifies internal traffic eligible for PAT.

**Key verification:**

```
show ip nat translations
show ip nat statistics
```

---

### 16. CDP and LLDP

**Purpose:** Disable Cisco-proprietary neighbor discovery (CDP) to reduce information exposure, while enabling the open-standard LLDP for controlled neighbor visibility.

CDP was disabled globally. LLDP was enabled globally, with LLDP transmit selectively disabled on access ports facing end-user devices.

```
show lldp neighbors
show cdp neighbors
```

---

### 17. Access Control Lists

**Purpose:** Enforce inter-VLAN traffic policies — specifically, allow ICMP between Office A and Office B PCs while blocking all other IP traffic between those subnets.

An extended named ACL was written and applied inbound on the Office A VLAN 10 SVI on DSW-A1. The ACL permits ICMP toward the Office B PC subnet, denies all other IP traffic to Office B PCs, and permits everything else.

**Expected behavior:**

| Source          | Destination     | ICMP  | Other IP |
|-----------------|-----------------|-------|----------|
| Office A PCs    | Office B PCs    | ✅ Allow | ❌ Deny |
| Office A PCs    | Any other       | ✅ Allow | ✅ Allow |

```
show access-lists
show running-config interface vlan 10
```

---

### 18. Port Security

**Purpose:** Restrict which MAC addresses may use an access port, using sticky learning to dynamically lock down learned MACs without manual entry.

Port security with sticky MAC learning and `restrict` violation mode was configured on all host-facing access ports. Maximum MAC counts were tuned per port type.

| Port Type              | Max MACs | Rationale                  |
|------------------------|----------|----------------------------|
| AP-facing ports        | 1        | One AP per port            |
| Phone + PC ports       | 2        | Phone MAC + PC MAC         |
| Server-facing ports    | 1        | Single server per port     |

`restrict` mode drops unauthorized frames and increments the violation counter without erring the port.

```
show port-security
show port-security interface <id>
show port-security address
```

---

### 19. DHCP Snooping

**Purpose:** Prevent rogue DHCP servers from assigning addresses to clients, and build a trusted binding table used by DAI for ARP validation.

DHCP snooping was enabled globally and per-VLAN on all access switches. Uplink ports toward distribution switches were marked trusted. All client-facing access ports remained untrusted. DHCP rate limiting was applied on untrusted ports. DHCP option 82 insertion was disabled for Packet Tracer compatibility.

```
show ip dhcp snooping
show ip dhcp snooping binding
```

---

### 20. Dynamic ARP Inspection

**Purpose:** Prevent ARP spoofing and ARP poisoning by validating ARP packets against the DHCP snooping binding table. Only ARP replies matching a known IP-to-MAC binding are forwarded.

DAI was enabled per-VLAN on access switches. Uplink ports were marked as DAI-trusted. ARP validation checks (src-mac, dst-mac, ip) were enabled for additional protection.

```
show ip arp inspection
show ip arp inspection interfaces
```

---

### 21. IPv6

**Purpose:** Demonstrate dual-stack configuration and IPv6 static routing on edge links.

IPv6 unicast routing was enabled on R1. Global unicast IPv6 addresses were assigned to routed interfaces using both manual addressing and EUI-64. A primary IPv6 default route and a floating backup were configured with differing administrative distances.

```
show ipv6 interface brief
show ipv6 route
ping ipv6 <destination>
```

---

### 22. Wireless LAN Controller

**Purpose:** Provide centralized wireless management for the campus, eliminating the need for per-AP configuration and enabling consistent WLAN policy enforcement.

WLC1 was configured with a management interface, a dynamic interface mapped to VLAN 40, and a WLAN profile. Lightweight APs registered to WLC1 via CAPWAP, discovering the controller using DHCP option 43. Wireless clients associate to the SSID, receive DHCP addresses, and route through DSW-A1/A2.

**WLAN settings:**

| Parameter     | Value            |
|---------------|------------------|
| SSID          | Wi-Fi            |
| Security      | WPA2-PSK         |
| VLAN          | 40               |
| Default GW    | 10.6.0.1 (HSRP)  |
| DHCP Server   | R1 (relayed)     |

**Verification:**

```
WLC > Monitor > AP Summary
WLC > Monitor > Clients
Wireless client: ipconfig
```

---

## Security Controls Summary

| Control                   | Applied On          | Protection Against                          |
|---------------------------|---------------------|---------------------------------------------|
| SSH + VTY ACL             | All devices         | Unauthorized / unencrypted management access |
| Enable secret + local auth | All devices        | Privilege escalation                         |
| BPDU Guard                | All access ports    | Unauthorized switch / STP manipulation       |
| Port Security (sticky)    | All access ports    | MAC flooding, unauthorized devices           |
| DHCP Snooping             | All access switches | Rogue DHCP servers, DHCP starvation          |
| Dynamic ARP Inspection    | All access switches | ARP spoofing, ARP poisoning                  |
| Extended ACL              | DSW-A1 VLAN 10 SVI  | Unauthorized inter-office traffic            |
| CDP disabled              | All devices         | Cisco topology information leakage           |
| SNMP read-only            | All devices         | Unauthorized configuration via SNMP writes   |

---

## Verification Checklist

### Layer 2 — Switching

```
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree
show spanning-tree root
show port-security
show port-security address
```

### Layer 3 — Routing and Redundancy

```
show ip interface brief
show ip route
show ip route ospf
show ip ospf neighbor
show ip ospf interface brief
show standby brief
show ipv6 interface brief
show ipv6 route
```

### Network Services

```
show ip dhcp pool
show ip dhcp binding
show ntp associations
show ntp status
show clock detail
show logging
show hosts
show ip nat translations
show ip nat statistics
```

### Security

```
show access-lists
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection interfaces
show ip ssh
show running-config | section line vty
```

### Wireless

```
WLC > Monitor > AP Summary
WLC > Monitor > Clients
Wireless client: ipconfig / ping
```

---

## Troubleshooting Notes

### Port Security Violations After Copying Devices

Packet Tracer may assign new MAC addresses to devices copied into a new workspace. Sticky MACs saved in the running config will no longer match, causing violations.

**Fix:** Clear the saved sticky addresses and allow the port to relearn.

```
show port-security interface f0/1
show port-security address
```

### OSPF Neighbor Not Forming

Common causes:

- Interface not enabled for OSPF (missing `network` statement or `ip ospf <id> area` command)
- Interface incorrectly set as passive
- Area ID mismatch between neighbors
- Duplicate router ID in the topology
- Subnet mask or MTU mismatch
- Network type mismatch (e.g. one side point-to-point, other broadcast)
- ACL blocking multicast 224.0.0.5/6

```
show ip ospf neighbor
show ip ospf interface brief
show ip protocols
```

### DHCP Clients Not Receiving Addresses

Common causes:

- Missing `ip helper-address` on the SVI
- DHCP snooping trust not configured on the uplink
- VLAN not in the trunk allowed list
- Wrong default router in the DHCP pool
- DHCP option 82 inserting a field the server rejects (disable with `no ip dhcp snooping information option`)

```
show ip dhcp binding
show ip dhcp snooping
show interfaces trunk
```

### HSRP Not Converging as Expected

Common causes:

- Wrong virtual IP configured (must match what end hosts use as default gateway)
- Missing `standby preempt` on the higher-priority switch
- SVI down due to VLAN not active or not allowed on trunk
- Priority values not differentiated between Active and Standby

```
show standby brief
show ip interface brief
show vlan brief
show interfaces trunk
```

---

## Repository Structure

```
ccna-enterprise-campus-mega-lab/
│
├── README.md
│
├── packet-tracer/
│   └── ccna-mega-lab.pkt
│
├── configs/
│   ├── R1.txt
│   ├── CSW1.txt
│   ├── CSW2.txt
│   ├── DSW-A1.txt
│   ├── DSW-A2.txt
│   ├── DSW-B1.txt
│   ├── DSW-B2.txt
│   ├── ASW-A1.txt
│   ├── ASW-A2.txt
│   ├── ASW-A3.txt
│   ├── ASW-B1.txt
│   ├── ASW-B2.txt
│   └── ASW-B3.txt
│
├── diagrams/
│   ├── logical-topology.png
│   ├── vlan-ip-plan.png
│   └── ospf-design.png
│
├── verification/
│   ├── ospf-neighbors.txt
│   ├── hsrp-status.txt
│   ├── etherchannel-summary.txt
│   ├── spanning-tree-root.txt
│   ├── dhcp-bindings.txt
│   ├── nat-translations.txt
│   └── security-outputs.txt
│
└── notes/
    ├── troubleshooting.md
    ├── lessons-learned.md
    └── command-reference.md
```

