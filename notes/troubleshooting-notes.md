# Troubleshooting Notes

## How to Use This File

Use this as a quick diagnosis guide while validating the CCNA Enterprise Campus Mega Lab. Start with the symptom, run the verification commands, then check the likely causes.

---

## 1. OSPF Neighbor Not Forming

### Symptoms

- `show ip ospf neighbor` is empty.
- Neighbor stuck in `INIT`, `2-WAY`, `EXSTART`, or `EXCHANGE`.
- Routes missing from `show ip route ospf`.

### Commands

```ios
show ip ospf neighbor
show ip ospf interface brief
show ip ospf interface <interface>
show ip protocols
show running-config | section router ospf
show running-config interface <interface>
show ip interface brief
```

### Common Causes

- Interface not enabled for OSPF.
- Wrong OSPF area.
- Passive interface configured on a neighbor-facing link.
- Subnet mask mismatch.
- Duplicate router ID.
- OSPF network type mismatch.
- Hello/dead timer mismatch.
- MTU mismatch.
- Interface down/down or VLAN/trunk issue.

### Fixes

```ios
router ospf 1
 router-id <loopback-ip>
 network <interface-ip> 0.0.0.0 area 0
 no passive-interface <neighbor-facing-interface>
```

For point-to-point routed links:

```ios
interface <interface>
 ip ospf network point-to-point
```

For R1 interface-based OSPF:

```ios
interface <interface>
 ip ospf 1 area 0
```

---

## 2. HSRP Gateway Not Working

### Symptoms

- PC cannot ping its default gateway.
- Wrong switch is active.
- `show standby brief` shows init/speak instead of active/standby.

### Commands

```ios
show standby brief
show ip interface brief
show vlan brief
show interfaces trunk
show running-config interface vlan <vlan-id>
```

### Common Causes

- SVI is down/down.
- VLAN missing.
- VLAN not allowed on trunk.
- HSRP virtual IP mismatch.
- Priority/preempt missing.
- Host default gateway set to physical SVI IP instead of HSRP VIP.

### Fix Example

```ios
interface vlan 10
 standby version 2
 standby 2 ip 10.1.0.1
 standby 2 priority 105
 standby 2 preempt
```

---

## 3. VLAN or Trunk Issue

### Symptoms

- Host cannot reach gateway.
- SVI stays down.
- DHCP fails.
- STP blocks unexpected link.

### Commands

```ios
show vlan brief
show interfaces trunk
show interfaces switchport
show spanning-tree vlan <vlan-id>
show mac address-table
```

### Common Causes

- VLAN not created.
- Access port assigned to wrong VLAN.
- Trunk missing allowed VLAN.
- Native VLAN mismatch.
- DTP negotiation mismatch.

### Fix Example

```ios
interface <interface>
 switchport mode trunk
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,40,99
 switchport nonegotiate
```

---

## 4. EtherChannel Not Bundling

### Symptoms

- `show etherchannel summary` does not show `(P)` member ports.
- Port-channel is down.
- Individual links are suspended.

### Commands

```ios
show etherchannel summary
show interfaces port-channel 1
show running-config interface port-channel 1
show running-config interface <member-interface>
```

### Common Causes

- Mismatched trunk settings.
- Mismatched allowed VLANs.
- Mismatched native VLAN.
- Wrong protocol/mode: PAgP vs LACP.
- Layer 2/Layer 3 mismatch.
- Port-channel configured after member ports inconsistently.

### Fix Example

```ios
interface range g1/0/4 - 5
 switchport mode trunk
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,40,99
 channel-group 1 mode desirable
```

---

## 5. DHCP Not Working

### Symptoms

- Client gets APIPA address.
- Client remains at 0.0.0.0.
- DHCP bindings missing on R1.

### Commands

```ios
show ip dhcp pool
show ip dhcp binding
show running-config | section dhcp
show running-config interface vlan <vlan-id>
show ip dhcp snooping
show ip dhcp snooping binding
```

### Common Causes

- Missing `ip helper-address` on SVI.
- DHCP pool default-router wrong.
- Wrong VLAN assignment.
- Trunk not allowing VLAN.
- DHCP snooping trust missing on uplinks.
- DHCP Option 82 issue in Packet Tracer.

### Fix Example

```ios
interface vlan 10
 ip helper-address 10.0.0.76
```

Packet Tracer-friendly DHCP snooping:

```ios
ip dhcp snooping
ip dhcp snooping vlan 10,20,40,99
no ip dhcp snooping information option
interface range g0/1 - 2
 ip dhcp snooping trust
```

---

## 6. Port Security Violation

### Symptoms

```text
%PORT_SECURITY-2-PSECURE_VIOLATION
```

### Commands

```ios
show port-security
show port-security interface f0/1
show port-security address
show running-config interface f0/1
show mac address-table interface f0/1
```

### Common Causes

- Sticky MAC from old copied Packet Tracer device.
- New end device has a different MAC address.
- Phone + PC port does not have maximum 2.
- Wrong device connected to secure port.

### Fix: Clear Sticky MAC and Relearn

For AP/server ports:

```ios
interface f0/1
 shutdown
 no switchport port-security mac-address sticky
 switchport port-security
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown
```

For phone + PC ports:

```ios
interface f0/1
 shutdown
 no switchport port-security mac-address sticky
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 no shutdown
```

---

## 7. NAT or Internet Access Fails

### Symptoms

- Internal clients cannot reach outside IPs.
- DNS resolves but ping fails.
- `show ip nat translations` is empty.

### Commands

```ios
show ip route
show ip route 0.0.0.0
show ip nat translations
show ip nat statistics
show access-lists
show running-config | include ip nat
```

### Common Causes

- Missing NAT inside/outside.
- NAT ACL missing subnet.
- NAT pool incorrect.
- No default route.
- OSPF default route not advertised.
- DNS issue mistaken for NAT issue.

### Fix Checklist

```ios
interface g0/0
 ip nat inside
interface g0/1
 ip nat inside
interface g0/0/0
 ip nat outside
interface g0/1/0
 ip nat outside
router ospf 1
 default-information originate
```

---

## 8. Wireless Client Issue

### Symptoms

- Laptop cannot join SSID.
- AP not joined to WLC.
- Wireless client gets wrong subnet.

### Checks

- PC can ping WLC management IP.
- APs have management IPs.
- DHCP Option 43 points to WLC.
- WLAN is enabled.
- WLAN maps to Wi-Fi dynamic interface.
- WPA2-PSK matches.
- Trunk to WLC allows VLAN 40 and native VLAN 99.

### Packet Tracer Note

Packet Tracer may occasionally assign an unexpected management-subnet IP to wireless clients even when the WLC mapping is correct. Verify configuration before assuming the design is wrong.
