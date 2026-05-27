# 02 - VLAN and IP Addressing Plan

## Office A VLAN/IP Plan

| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.0/28 | 10.0.0.1 |
| 10 | PCs | 10.1.0.0/24 | 10.1.0.1 |
| 20 | Phones | 10.2.0.0/24 | 10.2.0.1 |
| 40 | Wireless | 10.6.0.0/24 | 10.6.0.1 |

## Office B VLAN/IP Plan

| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.16/28 | 10.0.0.17 |
| 10 | PCs | 10.3.0.0/24 | 10.3.0.1 |
| 20 | Phones | 10.4.0.0/24 | 10.4.0.1 |
| 30 | Servers | 10.5.0.0/24 | 10.5.0.1 |

## Why VLAN Segmentation
VLAN segmentation reduces broadcast scope, improves fault isolation, and allows policy-based control between departments and services. It also supports clean QoS/security posture (for example, separating voice and user traffic) and aligns with hierarchical campus operations.
