# Wireless WLC/CAPWAP Flow

1. LWAP receives IP addressing (DHCP scope and Option 43 where required).
2. LWAP discovers WLC management IP and forms CAPWAP control session.
3. WLC pushes WLAN/SSID policy to joined AP.
4. Client authentication and VLAN mapping occur per WLAN profile.
5. Client traffic is centrally tunneled or locally switched depending on AP mode/FlexConnect design.

Verification artifacts for AP/client summary are pending in `verification/wireless/`.
