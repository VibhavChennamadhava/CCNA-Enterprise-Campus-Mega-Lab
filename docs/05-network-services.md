# 05 - Network Services

## Services Covered
- DHCP and DHCP relay
- DNS
- NTP
- Syslog
- SNMP
- FTP / IOS image management workflow
- SSH management access
- NAT/PAT
- CDP/LLDP controls

## Service Notes and Accuracy Constraints
- **DHCP:** Router-side pools are present for management/user/voice/wireless segments. A dedicated Office B server DHCP pool is **not asserted** unless future exported evidence proves it.
- **SRV1 addressing:** Documented as a **static server endpoint** unless configuration evidence shows otherwise.
- **SNMP:** SRV1 acts as the potential monitoring/NMS side; network devices act as SNMP agents. Published configs should keep SNMP communities redacted.
- **NAT/PAT:** NAT outside should reference all Internet-facing interfaces when dual-uplink/redundant links are configured; NAT inside should reference LAN/core-facing interfaces.
- **SSH:** SSHv2 was enforced, Telnet disabled, and VTY access restricted using an ACL. Exact RSA key size and permitted management subnets should be validated from direct command evidence.
- **NTP:** Configuration includes `ntp master <stratum>` behavior; exact operational stratum verification is pending command output evidence.
