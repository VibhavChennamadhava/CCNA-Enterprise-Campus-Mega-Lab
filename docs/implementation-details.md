# Implementation Details

## VLANs and Trunks
The access and distribution layers segment user, voice, wireless, server, and management traffic with VLANs 10, 20, 30, 40, and 99. Trunk links use explicit allowed VLAN lists and native VLAN controls, with DTP disabled on trunk interfaces.

## EtherChannel
- DSW-A1 ↔ DSW-A2: Layer 2 EtherChannel, PAgP desirable, Port-channel1
- DSW-B1 ↔ DSW-B2: Layer 2 EtherChannel, LACP active, Port-channel1
- CSW1 ↔ CSW2: Layer 3 EtherChannel, PAgP desirable, Port-channel1 (`10.0.0.41/30` and `10.0.0.42/30`)

## STP and HSRP Alignment
Rapid PVST+ was configured on the active Layer 2 switching domain/distribution-access layer. HSRP active gateways were aligned with STP root placement to keep forwarding paths deterministic per VLAN group.

## OSPF
OSPF process 1 in area 0 carries campus routes across routed edge/core/distribution links. R1 uses interface-level OSPF and originates the default route. Core and distribution switches use loopback-based router IDs and passive VLAN SVI strategy for user segments.

## DHCP and Relay
R1 hosts DHCP pools: A-Mgmt, A-PC, A-Phone, B-Mgmt, B-PC, B-Phone, and Wi-Fi. Distribution SVIs relay requests to R1 with helper addresses. SRV1 resides in VLAN 30 as a server endpoint, not a DHCP pool source.

## NAT/PAT
R1 uses NAT inside on G0/0 and G0/1, and NAT outside on G0/0/0 and G0/1/0. Static NAT publishes `10.5.0.4` as `203.0.113.113`. PAT uses pool `203.0.113.200-203.0.113.207` with ACL 2 permitting office user/wireless VLAN subnets.

## ACLs and Management Access
VTY access is restricted by ACL 1 (`permit 10.1.0.0/24`), SSHv2 is enforced, and Telnet is disabled via `transport input ssh`.

## Port Security, DHCP Snooping, and DAI
Access interfaces use sticky MAC port security and violation controls. DHCP Snooping and Dynamic ARP Inspection are enabled on user VLANs with trusted uplink interfaces.

## WLC/LWAP
WLC1 centrally manages LWAP1 and LWAP2 with CAPWAP. DHCP Option 43 provides WLC discovery for AP onboarding. Wireless clients are mapped to VLAN 40.

## IPv6
IPv6 unicast routing is enabled. R1, CSW1, and CSW2 include IPv6 addressing and default route logic, including primary and backup default behavior at the edge.
