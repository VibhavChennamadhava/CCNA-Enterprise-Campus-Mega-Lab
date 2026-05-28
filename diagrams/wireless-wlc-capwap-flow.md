# Wireless WLC/CAPWAP Flow

1. LWAP gets management addressing from DHCP.
2. DHCP option 43 advertises WLC1 management IP (10.0.0.7) for AP discovery.
3. LWAP forms CAPWAP control connectivity with WLC1.
4. WLC1 pushes WLAN/SSID settings and VLAN mapping.
5. Client traffic is centrally tunneled or locally switched based on AP mode (local/FlexConnect).
