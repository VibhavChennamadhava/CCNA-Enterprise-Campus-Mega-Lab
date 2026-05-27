# 03 - Layer 2 Switching Design

## Design Elements
- VLANs for user/voice/server/wireless/management separation
- Access ports mapped to data VLANs with voice VLAN where phones are present
- 802.1Q trunks with explicit allowed VLAN lists and defined native VLAN
- Rapid PVST+ as spanning-tree mode
- PortFast + BPDU Guard on host-facing ports
- EtherChannel for bandwidth and redundancy
- VTP usage: **to be validated from current evidence**

## EtherChannel Implementation
- **DSW-A1 ↔ DSW-A2:** Layer 2 EtherChannel using **PAgP desirable**
- **DSW-B1 ↔ DSW-B2:** Layer 2 EtherChannel using **LACP active**
- **CSW1 ↔ CSW2:** Layer 3 EtherChannel using **PAgP desirable** (validated from running configs; not static `mode on`)

## STP and HSRP Alignment
The design aligns STP root placement with HSRP active gateway roles per VLAN group to avoid suboptimal first-hop forwarding paths through a non-root/non-active distribution switch.
