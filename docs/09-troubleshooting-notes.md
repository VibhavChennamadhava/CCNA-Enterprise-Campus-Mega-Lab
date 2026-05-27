# 09 - Troubleshooting Notes

## OSPF Neighbor Not Forming
Check IP addressing, wildcard/network statements, area mismatch, passive-interface status, and interface MTU/authentication settings.

## HSRP Not Converging
Verify matching group numbers, virtual IP, version, timers, priority/preempt behavior, and SVI line protocol state.

## DHCP Clients Not Receiving Addresses
Validate relay helper addresses, excluded ranges, pool network/default-router correctness, and VLAN trunk propagation.

## Trunk Allowed VLAN Issues
Confirm required VLANs are in `allowed vlan` list on both ends and native VLAN is consistent.

## EtherChannel Member Mismatch
Compare channel protocol/mode, speed/duplex, trunk settings, and STP consistency. Remove/re-add misbound members when needed.

## Port Security Sticky MAC Issues
Review violation mode, maximum MAC count, aging behavior, and whether stale sticky entries must be cleared.

## DAI / DHCP Snooping Issues
Ensure trusted interfaces are correctly set, VLAN scope is enabled consistently, and bindings exist for inspected traffic.

## NAT Translations Not Appearing
Validate ACL match logic, NAT inside/outside interface role assignment, route reachability, and active traffic generation.

## WLC / AP Registration Issues
Check AP IP reachability, option 43/controller discovery, CAPWAP path, management VLAN tagging, and clock consistency.
