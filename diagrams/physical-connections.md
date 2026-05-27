# Physical and Logical Connection Reference

## R1
- G0/0 -> CSW1, 10.0.0.33/30
- G0/1 -> CSW2, 10.0.0.37/30
- G0/0/0 -> Primary ISP, NAT outside
- G0/1/0 -> Backup ISP, NAT outside

## CSW1
- G1/0/1 -> R1, 10.0.0.34/30
- G1/0/2 and G1/0/3 -> Port-channel1 to CSW2 using PAgP desirable
- Port-channel1 -> 10.0.0.41/30
- G1/1/1 -> routed uplink to DSW-A1, 10.0.0.45/30
- G1/1/2 -> routed uplink to DSW-A2, 10.0.0.49/30
- G1/1/3 -> routed uplink to DSW-B1, 10.0.0.53/30
- G1/1/4 -> routed uplink to DSW-B2, 10.0.0.57/30

## DSW-A1
- G1/1/1 -> routed uplink to CSW1, 10.0.0.46/30
- G1/1/2 -> routed uplink toward CSW2, 10.0.0.62/30
- G1/0/4 and G1/0/5 -> Port-channel1 to DSW-A2 using PAgP desirable
- G1/0/1 through G1/0/3 -> trunks toward access block per Packet Tracer topology

## DSW-B1
- G1/1/1 -> routed uplink to CSW1, 10.0.0.54/30
- G1/1/2 -> routed uplink toward CSW2, 10.0.0.70/30
- G1/0/4 and G1/0/5 -> Port-channel1 to DSW-B2 using LACP active
- G1/0/1 through G1/0/3 -> trunks toward access block per Packet Tracer topology

## ASW-A1
- F0/1 -> LWAP1, access VLAN 99
- F0/2 -> WLC1, trunk native VLAN 99, allowed VLANs 40 and 99
- G0/1 and G0/2 -> distribution uplinks, trunk native VLAN 1000, allowed VLANs 10,20,40,99

## ASW-B1
- F0/1 -> LWAP2, access VLAN 99
- G0/1 and G0/2 -> distribution uplinks, trunk native VLAN 1000, allowed VLANs 10,20,30,99
