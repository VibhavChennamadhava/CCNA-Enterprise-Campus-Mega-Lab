# Lessons Learned

This lab reinforced that campus networking is mostly about consistency across layers, not isolated feature configuration. The biggest practical challenge was keeping L2 path control (STP), first-hop redundancy (HSRP), and L3 path selection (OSPF/default routing) aligned during failure testing.

Working through DHCP relay, NAT/PAT, and access security together made it clear how quickly small mismatches in VLAN scope or trust boundaries create user-facing outages. The troubleshooting approach that worked best was strict layering: verify interface/VLAN state first, then gateway and routing state, then service-policy behavior.

Compared with a simple CCNA feature lab, this project improved operational discipline: documenting assumptions, validating each control plane, and keeping configurations reviewable. In a production environment, the next step would be stronger change-control artifacts, centralized configuration backups, and time-sequenced verification captures for every major failover scenario.
