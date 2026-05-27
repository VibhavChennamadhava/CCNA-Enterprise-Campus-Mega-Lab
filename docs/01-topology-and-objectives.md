# 01 - Topology and Objectives

This lab implements a **three-tier enterprise campus model**: core, distribution, and access. The design objective is to show end-to-end interoperability between Layer 2, Layer 3, services, security, and wireless functions.

## Topology Roles
- **Edge (R1):** Internet edge, NAT/PAT, DHCP services, and default route source.
- **Core (CSW1/CSW2):** High-speed L3 transit and OSPF backbone interconnection.
- **Distribution (DSW-A1/A2, DSW-B1/B2):** SVIs, first-hop redundancy (HSRP), policy control, and STP root control.
- **Access (ASW-A1/A2/A3, ASW-B1/B2/B3):** Endpoint/AP attachment, VLAN enforcement, and access security features.
- **Wireless (WLC1 + LWAPs):** Centralized AP control with CAPWAP-based management.
- **Server/Services (SRV1):** DNS/Syslog/NMS roles as configured in lab scope.
- **Endpoints:** PCs, IP phones, and wireless clients for validation traffic.

## Objectives
1. Build a resilient campus hierarchy with redundant forwarding paths.
2. Segment user, voice, server, wireless, and management traffic by VLAN.
3. Provide deterministic gateway availability with HSRP and aligned STP roots.
4. Route dynamically with OSPF across the campus core/distribution topology.
5. Implement realistic operations/security controls and verify with command evidence.

## Source Attribution
This project is inspired by Jeremy's IT Lab CCNA Mega Lab, but all documentation here is written in original wording and structured as a portfolio artifact.
