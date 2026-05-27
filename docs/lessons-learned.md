# Lessons Learned

Building this lab as a full campus system was most useful when each feature was validated in sequence instead of configured in isolation. The main challenge was keeping Layer 2 forwarding behavior, HSRP active gateways, and OSPF routing decisions aligned after changes.

The most effective troubleshooting approach was to move from physical/link checks to VLAN and trunk state, then to FHRP/routing, and finally to services and policy enforcement. That sequence reduced guesswork and made root-cause isolation faster.

This project improved practical understanding of EtherChannel consistency requirements, STP/HSRP role planning, DHCP relay behavior, NAT inside/outside role mapping, and how wireless AP onboarding depends on management-plane reachability.

For a production environment, the next step would be formalized change-control templates, automated config backups, and centralized monitoring baselines for routing, security, and wireless health.
