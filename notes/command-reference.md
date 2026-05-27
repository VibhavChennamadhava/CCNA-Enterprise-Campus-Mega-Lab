# CCNA Mega Lab Command Reference

## Basic IOS

```ios
enable
configure terminal
end
write memory
copy running-config startup-config
show running-config
show startup-config
show ip interface brief
```

## Device Access and Hardening

```ios
hostname <name>
enable secret <password>
username <user> secret <password>
line console 0
 login local
 exec-timeout 30 0
 logging synchronous
line vty 0 15
 login local
 transport input ssh
 access-class 1 in
```

## SSH

```ios
ip domain-name jeremysitlab.com
crypto key generate rsa
ip ssh version 2
show ip ssh
show users
```

## VLANs

```ios
vlan 10
 name PCs
interface f0/1
 switchport mode access
 switchport access vlan 10
show vlan brief
```

## Voice VLAN

```ios
interface f0/1
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 20
```

## Trunks

```ios
interface g0/1
 switchport mode trunk
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,40,99
 switchport nonegotiate
show interfaces trunk
```

## EtherChannel

### PAgP

```ios
interface range g1/0/4 - 5
 channel-group 1 mode desirable
show etherchannel summary
```

### LACP

```ios
interface range g1/0/4 - 5
 channel-group 1 mode active
show etherchannel summary
```

### Layer 3 EtherChannel

```ios
interface range g1/0/2 - 3
 no switchport
 channel-group 1 mode desirable
interface port-channel1
 no switchport
 ip address 10.0.0.41 255.255.255.252
```

## STP

```ios
spanning-tree mode rapid-pvst
spanning-tree vlan 10,99 priority 0
spanning-tree vlan 20,40 priority 4096
show spanning-tree
show spanning-tree root
show spanning-tree vlan 10
```

## PortFast and BPDU Guard

```ios
interface f0/1
 spanning-tree portfast
 spanning-tree bpduguard enable
```

For trunk to WLC:

```ios
interface f0/2
 spanning-tree portfast trunk
 spanning-tree bpduguard enable
```

## Inter-VLAN Routing / SVIs

```ios
ip routing
interface vlan 10
 ip address 10.1.0.2 255.255.255.0
 no shutdown
```

## HSRP

```ios
interface vlan 10
 standby version 2
 standby 2 ip 10.1.0.1
 standby 2 priority 105
 standby 2 preempt
show standby brief
```

## OSPF

### Router process

```ios
router ospf 1
 router-id 10.0.0.77
 passive-interface loopback0
 network 10.0.0.77 0.0.0.0 area 0
```

### Interface-based OSPF

```ios
interface g0/0
 ip ospf 1 area 0
 ip ospf network point-to-point
```

### Verification

```ios
show ip ospf neighbor
show ip ospf interface brief
show ip route ospf
show ip protocols
```

## Static Default Routes

```ios
ip route 0.0.0.0 0.0.0.0 203.0.113.1
ip route 0.0.0.0 0.0.0.0 203.0.113.5 2
```

## OSPF Default Route Advertisement

```ios
router ospf 1
 default-information originate
```

## DHCP Server

```ios
ip dhcp excluded-address 10.1.0.1 10.1.0.10
ip dhcp pool A-PC
 network 10.1.0.0 255.255.255.0
 default-router 10.1.0.1
 dns-server 10.5.0.4
 domain-name jeremysitlab.com
show ip dhcp pool
show ip dhcp binding
```

## DHCP Relay

```ios
interface vlan 10
 ip helper-address 10.0.0.76
```

## DNS Client

```ios
ip domain-name jeremysitlab.com
ip name-server 10.5.0.4
show hosts
```

## NTP

```ios
ntp master 5
ntp authentication-key 1 md5 <key>
ntp trusted-key 1
ntp server 10.0.0.76 key 1
show ntp associations
show ntp status
```

## Syslog

```ios
logging 10.5.0.4
logging trap debugging
logging buffered 8192
show logging
```

## SNMP

```ios
snmp-server community <community> ro
show running-config | include snmp
```

## NAT

```ios
interface g0/0
 ip nat inside
interface g0/0/0
 ip nat outside
ip nat pool POOL1 203.0.113.200 203.0.113.207 netmask 255.255.255.248
access-list 2 permit 10.1.0.0 0.0.0.255
ip nat inside source list 2 pool POOL1 overload
ip nat inside source static 10.5.0.4 203.0.113.113
show ip nat translations
show ip nat statistics
```

## ACLs

```ios
ip access-list extended OfficeA_to_OfficeB
 permit icmp 10.1.0.0 0.0.0.255 10.3.0.0 0.0.0.255
 deny ip 10.1.0.0 0.0.0.255 10.3.0.0 0.0.0.255
 permit ip any any
interface vlan 10
 ip access-group OfficeA_to_OfficeB in
show access-lists
```

## Port Security

```ios
interface f0/1
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
show port-security
show port-security interface f0/1
show port-security address
```

## DHCP Snooping

```ios
ip dhcp snooping
ip dhcp snooping vlan 10,20,40,99
no ip dhcp snooping information option
interface g0/1
 ip dhcp snooping trust
interface f0/1
 ip dhcp snooping limit rate 15
show ip dhcp snooping
show ip dhcp snooping binding
```

## Dynamic ARP Inspection

```ios
ip arp inspection vlan 10,20,40,99
ip arp inspection validate dst-mac src-mac ip
interface g0/1
 ip arp inspection trust
show ip arp inspection
show ip arp inspection interfaces
```

## CDP and LLDP

```ios
no cdp run
lldp run
interface f0/1
 no lldp transmit
show cdp neighbors
show lldp neighbors
```

## IPv6

```ios
ipv6 unicast-routing
interface g0/0
 ipv6 address 2001:db8:a1::/64 eui-64
ipv6 route ::/0 2001:db8:a::1
ipv6 route ::/0 g0/1/0 2001:db8:b::1 2
show ipv6 interface brief
show ipv6 route
```

## WLC GUI Reference

```text
Access WLC:
https://10.0.0.7

Dynamic interface:
Controller > Interfaces > New

WLAN:
WLANs > Create New

Security:
WPA2-PSK / AES / PSK cisco123
```
