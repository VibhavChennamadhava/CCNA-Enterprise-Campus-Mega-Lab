# Implementation Details

## VLANs and Trunks
User, voice, server, wireless, and management traffic is segmented with VLANs 10/20/30/40/99. Access uplinks are 802.1Q trunks with native VLAN 1000 (or VLAN 99 toward WLC trunking on ASW-A1 F0/2), explicit allowed VLAN lists, and DTP disabled.

## EtherChannel
- DSW-A1 ↔ DSW-A2: Layer 2 EtherChannel (Port-channel1), PAgP desirable
- DSW-B1 ↔ DSW-B2: Layer 2 EtherChannel (Port-channel1), LACP active
- CSW1 ↔ CSW2: Layer 3 EtherChannel (Port-channel1), PAgP desirable, addressed as 10.0.0.41/30 and 10.0.0.42/30

## STP and HSRP Alignment
Rapid PVST+ is used on the active Layer 2 switching domain (distribution/access). STP root roles are aligned with HSRP active gateway roles per VLAN group to keep first-hop forwarding paths deterministic.

## OSPF
OSPF process 1 runs in area 0. R1 uses interface-level OSPF on Loopback0, G0/0, and G0/1, and originates the default route with `default-information originate`. Core/distribution use loopback-based router IDs, host-specific wildcard statements where configured, and passive VLAN SVIs on distribution switches.

## DHCP and Relay
R1 provides DHCP pools:
- A-Mgmt, A-PC, A-Phone
- B-Mgmt, B-PC, B-Phone
- Wi-Fi

Distribution SVIs use helper addresses toward R1 where required. SRV1 is a server endpoint in VLAN 30 and is not documented as a DHCP scope.

## NAT/PAT
NAT inside:
- R1 GigabitEthernet0/0
- R1 GigabitEthernet0/1

NAT outside:
- R1 GigabitEthernet0/0/0 (Primary ISP)
- R1 GigabitEthernet0/1/0 (Backup ISP)

Policies:
- Static NAT: 10.5.0.4 → 203.0.113.113
- PAT pool: 203.0.113.200–203.0.113.207 (/29)
- ACL 2 permits 10.1.0.0/24, 10.2.0.0/24, 10.3.0.0/24, 10.4.0.0/24, 10.6.0.0/24

## ACLs and Management Access
SSH version 2 is enforced. Telnet is disabled with `transport input ssh` and VTY access is restricted by ACL 1, which permits 10.1.0.0/24.

## Port Security
Host-facing access ports use sticky MAC learning and violation restrict mode to limit unauthorized endpoint swaps.

## DHCP Snooping and DAI
DHCP Snooping and Dynamic ARP Inspection are enabled on access switching, with trusted uplink ports toward the distribution layer.

## WLC/LWAP Design
WLC1 is connected in Office A (ASW-A1). LWAP1 connects to ASW-A1 and LWAP2 connects to ASW-B1. DHCP option 43 in management scopes points AP discovery to 10.0.0.7. Client traffic is carried on WLAN VLAN 40.

## IPv6
IPv6 unicast routing is enabled. R1 uses global unicast addressing on uplinks with default and floating default routes, and core routing includes IPv6-enabled routed interfaces.
