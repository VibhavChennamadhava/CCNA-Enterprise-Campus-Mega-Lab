# Wireless WLC and CAPWAP Flow

- WLC1 is located in Office A and connected to ASW-A1.
- LWAP1 is connected to ASW-A1.
- LWAP2 is connected to ASW-B1.
- AP management onboarding uses DHCP and Option 43 pointing to WLC1 at `10.0.0.7`.
- LWAPs establish CAPWAP control sessions to WLC1.
- Wireless clients join the configured WLAN and are mapped to VLAN 40 for user data traffic.
