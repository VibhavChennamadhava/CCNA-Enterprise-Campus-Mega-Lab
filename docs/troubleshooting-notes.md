# Troubleshooting Notes

## OSPF Neighbor Not Forming
- Confirm both sides are in area 0 with matching IP/mask.
- Verify interface is not passive and OSPF network type is appropriate.
- Check MTU/authentication mismatches and link state.

## HSRP Not Converging
- Check group number, virtual IP, and HSRP version consistency.
- Verify priority/preempt settings and SVI line protocol status.

## DHCP Clients Not Receiving Addresses
- Confirm helper addresses on SVIs.
- Validate pool subnet/default-router values and excluded address ranges.
- Confirm VLAN is allowed on all required trunks.

## Trunk Allowed VLAN Issues
- Verify both trunk ends include required VLANs.
- Check native VLAN consistency and trunk mode settings.

## EtherChannel Member Mismatch
- Confirm channel protocol/mode parity and trunk settings on all members.
- Remove/re-add mismatched members and recheck `show etherchannel summary`.

## Port Security Sticky MAC Issues
- Clear stale secure MAC entries when moving endpoints.
- Verify violation mode and maximum MAC limits.

## DAI / DHCP Snooping Issues
- Confirm trusted uplinks are correctly configured.
- Verify snooping VLAN list includes all inspected VLANs.

## NAT Translations Not Appearing
- Confirm NAT inside/outside interface roles.
- Check ACL 2 match logic and active traffic generation.
- Verify upstream reachability and default route path.

## WLC / AP Registration Issues
- Verify AP management VLAN reachability and DHCP option 43.
- Confirm AP can reach WLC1 and establish CAPWAP.
