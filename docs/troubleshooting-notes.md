# Troubleshooting Notes

## OSPF Neighbor Not Forming
- Verify both sides are in area 0 and interface addressing is correct.
- Confirm `ip ospf network point-to-point` on routed links where configured.
- Check passive-interface settings so only intended interfaces form adjacencies.

## HSRP Not Converging
- Check VLAN SVI status/up state on both distribution switches.
- Verify matching HSRP group, VIP, and version.
- Review priority/preempt alignment with design intent.

## DHCP Clients Not Receiving Addresses
- Confirm helper-address configuration on VLAN SVIs.
- Verify DHCP pool subnet/default-router values and excluded ranges on R1.
- Confirm trunks carry required VLANs end-to-end.

## Trunk Allowed VLAN Issues
- Compare allowed VLAN lists on both sides.
- Validate native VLAN consistency and DTP disabled settings.

## EtherChannel Member Mismatch
- Ensure matching mode/protocol across members (PAgP/LACP).
- Check per-member trunk properties and speed/duplex consistency.

## Port Security Sticky MAC Issues
- Review sticky MAC table and violation mode.
- Clear stale sticky entries when moving endpoints.

## DAI / DHCP Snooping Issues
- Verify Snooping is enabled on VLANs in scope.
- Ensure uplinks are trusted and host ports are untrusted.
- Confirm Snooping bindings exist for inspected hosts.

## NAT Translations Not Appearing
- Generate matching inside traffic first.
- Validate ACL 2 match coverage and inside/outside interface roles.
- Confirm default route reachability toward active ISP path.

## WLC / AP Registration Issues
- Verify AP management VLAN connectivity and addressing.
- Confirm DHCP option 43 points to WLC1 (10.0.0.7).
- Check CAPWAP join status and WLAN/VLAN mapping.
