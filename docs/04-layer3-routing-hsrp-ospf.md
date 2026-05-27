# 04 - Layer 3 Routing, HSRP, and OSPF

## Layer 3 Design Summary
- Routed links are used for core/distribution and edge/core transit.
- Distribution switches host VLAN SVIs and HSRP virtual gateways.
- OSPF is used for internal dynamic routing with loopback router IDs.
- Passive interfaces are used on non-routing-facing interfaces.
- OSPF point-to-point style routed links are used in backbone/transit design.
- Default route injection originates from the edge routing domain.

## HSRP Active / STP Root Roles

| VLAN Scope | Active HSRP | STP Root |
|---|---|---|
| Office A VLANs 10 and 99 | DSW-A1 | DSW-A1 |
| Office A VLANs 20 and 40 | DSW-A2 | DSW-A2 |
| Office B VLANs 10 and 99 | DSW-B1 | DSW-B1 |
| Office B VLANs 20 and 30 | DSW-B2 | DSW-B2 |

This role symmetry supports predictable convergence and cleaner east-west forwarding behavior in failure scenarios.
