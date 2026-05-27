# OSPF Routing Design

- OSPF process: `1`
- Area: `0` across edge, core, and distribution routed links
- Point-to-point OSPF network type is configured on routed transit interfaces
- R1 injects default route with `default-information originate`
- Distribution switches use passive user VLAN SVIs while keeping routed uplinks active for adjacencies

## Router IDs
- R1: `10.0.0.76`
- CSW1: `10.0.0.77`
- CSW2: `10.0.0.78`
- DSW-A1: `10.0.0.79`
- DSW-A2: `10.0.0.80`
- DSW-B1: `10.0.0.81`
- DSW-B2: `10.0.0.82`

## Implementation Notes
- R1 runs interface-level OSPF on Loopback0, G0/0, and G0/1.
- CSW1 uses host-specific `network x.x.x.x 0.0.0.0 area 0` statements for each routed address.
- DSW-A1 and DSW-B1 advertise loopback IDs and routed interfaces, with user SVIs marked passive.
