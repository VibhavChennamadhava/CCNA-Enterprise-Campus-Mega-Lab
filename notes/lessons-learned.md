# Lessons Learned

## 1. Network Documentation Matters

A working network is not enough. A network should be documented with topology diagrams, VLAN/IP tables, device roles, verification evidence, and troubleshooting notes. This makes the network easier to review, explain, and troubleshoot.

## 2. Build in Layers

The most reliable approach was to configure and verify the network in layers:

1. Basic device setup
2. VLANs and trunks
3. EtherChannel
4. SVIs and HSRP
5. STP alignment
6. OSPF routing
7. IP services
8. NAT and Internet access
9. Security controls
10. Wireless
11. IPv6

When something failed, checking the previous layer first saved time.

## 3. HSRP and STP Should Be Aligned

The active HSRP gateway should usually be the STP root for the same VLAN. This keeps traffic forwarding toward the active gateway instead of taking an inefficient Layer 2 path.

## 4. Passive Interfaces Reduce Noise

OSPF passive interfaces are important on user VLANs. The network still advertises those connected subnets, but it avoids unnecessary OSPF hello packets and neighbor relationships on end-user segments.

## 5. Exact OSPF Network Statements Make Troubleshooting Easier

Using `network <interface-ip> 0.0.0.0 area 0` on switches made it clear exactly which interfaces were enabled for OSPF.

## 6. Point-to-Point OSPF Is Cleaner on Two-Device Routed Links

Ethernet defaults to OSPF broadcast network type, which elects a DR and BDR. For direct routed links between two devices, changing the OSPF network type to point-to-point avoids unnecessary DR/BDR behavior and produces cleaner neighbor output.

## 7. DHCP Relay Is Required Across VLANs

Clients send DHCP Discover messages as broadcasts, and routers do not forward broadcasts by default. The `ip helper-address` command on each SVI allows clients in different VLANs to reach the centralized DHCP server.

## 8. Port Security Can Break After Copying Packet Tracer Devices

Sticky MAC addresses can remain in the configuration. When devices are copied into a new Packet Tracer workspace, their MAC addresses may change, causing port-security violations. Clearing sticky MACs and allowing the switch to relearn the current device MAC resolves this.

## 9. DHCP Snooping and DAI Work Together

DHCP snooping builds the trusted IP-to-MAC binding table. Dynamic ARP Inspection uses that table to validate ARP packets and block ARP spoofing attempts.

## 10. Verification Commands Are as Important as Configuration Commands

The most useful commands in this lab were:

```ios
show ip interface brief
show interfaces trunk
show etherchannel summary
show spanning-tree root
show standby brief
show ip ospf neighbor
show ip route
show ip dhcp binding
show ip nat translations
show port-security
show ip dhcp snooping binding
show ip arp inspection
```

## 11. Sanitize Configs Before Publishing

Configuration files can contain passwords, SNMP community strings, NTP keys, and other sensitive information. Public GitHub repositories should use placeholders or sanitized values instead of real secrets.
