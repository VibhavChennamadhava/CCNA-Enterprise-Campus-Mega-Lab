# 06 - Security Controls

## Control Summary

| Control | Intent | Status Notes |
|---|---|---|
| SSH-only management | Encrypt remote administration; disable Telnet | Implemented in configs; verify with `show ip ssh` evidence |
| VTY ACL | Restrict management-plane source access | Present in configs; subnet scope should be evidence-backed |
| BPDU Guard + PortFast | Protect access ports against rogue bridge insertion | Expected on host-facing ports; verify per-switch outputs |
| Port Security | Limit/stick endpoint MAC addresses | Validation outputs should be captured in verification/security |
| DHCP Snooping | Block rogue DHCP responses | Verification evidence pending or partial |
| Dynamic ARP Inspection | Block ARP spoofing using snooping bindings | Verification evidence pending or partial |
| Extended ACLs | Enforce inter-segment policy controls | Capture `show access-lists` outputs |
| CDP disabled / LLDP enabled (where applicable) | Reduce unnecessary L2 discovery exposure while keeping standards-based visibility | Validate interface-level behavior from configs/outputs |
| SNMP read-only | Monitoring without write access risk | RO communities present; keep values redacted |
