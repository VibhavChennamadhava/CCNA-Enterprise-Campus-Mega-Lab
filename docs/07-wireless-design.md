# 07 - Wireless Design

## Wireless Components
- **WLC1** provides centralized WLAN definitions and AP control.
- **LWAPs** discover/join the WLC using management connectivity and CAPWAP.
- DHCP option 43 can be used to direct AP discovery in routed topologies.
- WLAN-to-VLAN mapping ties SSID traffic to campus VLAN design.

## Design Notes
- WPA2-PSK WLAN operation is part of lab scope; published keys must always be redacted.
- Office B AP client traffic may be centrally tunneled to WLC or locally switched depending on AP mode/FlexConnect behavior.
- Because complete WLC screenshots/exports are not fully present in this repository yet, wireless claims are intentionally limited to design intent plus known implemented components.
