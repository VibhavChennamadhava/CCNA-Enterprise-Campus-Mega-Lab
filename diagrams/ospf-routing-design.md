# OSPF Routing Design

## OSPF Domain
- OSPF process: `1`
- Backbone area: `0`
- Routed links use point-to-point network type where configured.
- R1 injects default route with `default-information originate`.

## Router IDs
- R1: 10.0.0.76
- CSW1: 10.0.0.77
- CSW2: 10.0.0.78
- DSW-A1: 10.0.0.79
- DSW-A2: 10.0.0.80
- DSW-B1: 10.0.0.81
- DSW-B2: 10.0.0.82

## Design Notes
- R1 uses interface-level OSPF on Loopback0, G0/0, and G0/1.
- CSW1 uses host-specific wildcard statements (`0.0.0.0`) for routed interfaces.
- DSW-A1 and DSW-B1 use loopbacks for stable router IDs and set user VLAN SVIs as passive interfaces.
- The design keeps user VLANs advertised while preventing unnecessary neighbor formation on access-facing segments.
