# CCNA Enterprise Campus Mega Lab

This repository documents a multi-site Cisco Packet Tracer enterprise campus lab built to integrate switching, routing, services, security, wireless, and IPv6 in one design. It is structured as a technical portfolio project with traceable design notes, device configurations, and verification evidence. Where evidence is not yet exported, the repository uses explicit placeholders instead of fabricated output.

## Skills Demonstrated
- Three-tier campus architecture (core/distribution/access)
- VLAN segmentation, trunking, EtherChannel, Rapid PVST+
- Inter-VLAN routing with HSRP and OSPF area 0 design
- DHCP relay, NAT/PAT, SSH hardening, SNMP/Syslog/NTP services
- Access-layer protections (Port Security, DHCP Snooping, Dynamic ARP Inspection)
- Wireless integration concepts (WLC/LWAP/CAPWAP)
- Structured troubleshooting and evidence collection

## Architecture Summary
- **Edge:** R1 provides WAN edge, NAT/PAT, DHCP, and default-route distribution.
- **Core:** CSW1/CSW2 provide L3 transit and OSPF backbone connectivity.
- **Distribution:** DSW-A1/A2 and DSW-B1/B2 host SVIs, HSRP gateways, STP root roles, and policy controls.
- **Access:** ASW-A1/A2/A3 and ASW-B1/B2/B3 connect endpoints, APs, and server access VLANs.
- **Services/Wireless:** SRV1 and WLC1 provide centralized network services and WLAN control.

## Key Technologies
EtherChannel (PAgP/LACP), Rapid PVST+, HSRP, OSPFv2, NAT/PAT, DHCP relay, SSHv2, ACLs, Port Security, DHCP Snooping, DAI, SNMP, Syslog, NTP, CAPWAP, IPv6 routing design.

## Validation Evidence
- Verification index: [`verification/README.md`](verification/README.md)
- Layer 2: [`verification/layer2/`](verification/layer2/)
- Layer 3: [`verification/layer3/`](verification/layer3/)
- Services: [`verification/services/`](verification/services/)
- Security: [`verification/security/`](verification/security/)
- Wireless placeholders: [`verification/wireless/`](verification/wireless/)

## Repository Map
- Top-level project overview: `README.md`
- Packet Tracer artifact: `packet-tracer/`
- Technical design docs: `docs/`
- Sanitized running configurations: `configs/`
- Topology/design diagrams: `diagrams/`
- Command/output evidence: `verification/`
- Screenshot guidance: `assets/screenshots/`

## How to Review This Lab
1. Read [`docs/01-topology-and-objectives.md`](docs/01-topology-and-objectives.md).
2. Review VLAN/IP and L2/L3 design in docs 02–04.
3. Review services/security/wireless/IPv6 in docs 05–08.
4. Cross-check implemented troubleshooting approach in [`docs/09-troubleshooting-notes.md`](docs/09-troubleshooting-notes.md).
5. Validate claims against `configs/` and `verification/` evidence.

## Security and Sanitization Note
Published configs are sanitized. Secrets (enable secrets, user passwords, SNMP communities, PSKs, keys, and credentials) must remain redacted using placeholders such as `<REDACTED_ENABLE_SECRET>` before any public sharing.

## Known Limitations / To Be Added
- Additional verification artifacts are still needed for full command checklist coverage (NTP status, Syslog output, SSH status, DHCP Snooping, DAI, and wireless command outputs).
- Packet Tracer screenshots are still pending in `assets/screenshots/`.
- IPv6 verification evidence is pending; current IPv6 section includes implementation intent and observed config elements where available.
