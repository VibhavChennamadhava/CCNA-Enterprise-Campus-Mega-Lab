# OSPF Routing Design

- OSPF process runs in area 0 across edge, core, and distribution routed links.
- Loopback interfaces are used as stable router IDs.
- User-facing SVIs are treated as passive where applicable.
- Edge device injects default route into OSPF domain.
- Routed point-to-point links minimize DR/BDR complexity and simplify failure domain behavior.

> Validate exact interface network statements and adjacency matrix with `show ip ospf neighbor` and running configs.
