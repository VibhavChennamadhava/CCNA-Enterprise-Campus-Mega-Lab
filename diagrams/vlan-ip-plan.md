# VLAN and IP Plan

## Office A
| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.0/28 | 10.0.0.1 |
| 10 | PCs | 10.1.0.0/24 | 10.1.0.1 |
| 20 | Phones | 10.2.0.0/24 | 10.2.0.1 |
| 40 | Wireless | 10.6.0.0/24 | 10.6.0.1 |

## Office B
| VLAN | Purpose | Subnet | HSRP VIP |
|---|---|---|---|
| 99 | Management | 10.0.0.16/28 | 10.0.0.17 |
| 10 | PCs | 10.3.0.0/24 | 10.3.0.1 |
| 20 | Phones | 10.4.0.0/24 | 10.4.0.1 |
| 30 | Servers | 10.5.0.0/24 | 10.5.0.1 |
