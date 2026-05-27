# HSRP and STP Alignment

| VLAN Scope | HSRP Active | STP Root | Reason |
|---|---|---|---|
| Office A VLANs 10, 99 | DSW-A1 | DSW-A1 | Align L2 root with active default gateway to avoid suboptimal forwarding |
| Office A VLANs 20, 40 | DSW-A2 | DSW-A2 | Split load while keeping gateway/root symmetry |
| Office B VLANs 10, 99 | DSW-B1 | DSW-B1 | Deterministic forwarding and fast gateway convergence |
| Office B VLANs 20, 30 | DSW-B2 | DSW-B2 | Balanced active role distribution across distribution pair |
