# 10 - Lessons Learned

## What Was Built
A complete campus lab integrating L2 segmentation, L3 redundancy/routing, shared services, and access security controls across two office blocks.

## What Was Difficult
Maintaining consistency across STP, HSRP, OSPF, and security features while preserving predictable failover behavior required careful staged validation.

## Troubleshooting Methods Used
- Layered verification (interface/VLAN first, then routing/services/security)
- Command-to-design cross-checking
- Incremental change testing with rollback points
- Comparing intended topology against observed control-plane state

## CCNA Concepts That Became Clearer
- Why STP root and HSRP active alignment materially affects forwarding paths
- How OSPF passive interfaces reduce control-plane noise without breaking reachability
- How DHCP Snooping and DAI are operationally linked
- Why evidence capture matters as much as configuration steps

## Improvements for a Real Enterprise Deployment
- Add centralized config backup/version control workflow
- Expand monitoring/alerting baselines and time-synchronization validation
- Add formal change window and test plan templates
- Include high-availability validation runbooks and documented RTO/RPO targets
