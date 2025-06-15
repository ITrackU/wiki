---
title: Cisco Routing
description: Commands and Configuration CCNA 1,2,3 for Cisco Routers
published: true
date: 2025-04-16T12:55:37.848Z
tags: router, routing, cisco, snmp, ntp, gre, tunnel, vpn, dynamic routing, static routing, bgp, rip, ospf
editor: markdown
dateCreated: 2025-04-16T12:07:21.555Z
---

# Basic Router Configuration
TasksCommandsHardware Name
```plaintext
Router(config)# hostname @router_name
```
Configure a password for privileged mode access
```plaintext
R1(config)# enable secret password
```
Configure console port access
```plaintext
R1(config)# line console 0
R1(config-line)# password password
R1(config-line)# login
R1(config-line)# exit
```
Configure VTY line access
```plaintext
R1(config)# line aux 0 4
R1(config-line)# password password
R1(config-line)# login
R1(config-line)# exit
```
Enable password encryption
```plaintext
R1(config)# service password-encryption
```
Configure a welcome banner
```plaintext
R1(config)# banner motd #Authorized Access Only!#
```
Save configurations
```plaintext
R1# copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```
# Interface Configuration
TasksCommandsEnter interface configuration mode (physical interface)
```plaintext
R1(config)# interface @iface@id
R1(config-if)# ip address @IP @mask
R1(config-if)# ipv6 address @ipv6/mask
R1(config-if)# description Link to
R1(config-if)# no shutdown
R1(config-if)# exit
```
Configure a loopback
```plaintext
R1(config)# interface loopback @id
R1(config-if)# ip address @ip @mask
R1(config-if)# exit
%LINEPROTO-5-UPDOWN:
Line protocol on Interface Loopback0, changed state to up
```
## Verify Interfaces and Routes
TasksCommandsDisplay IPv4 interface status
```plaintext
R1# show ip interface brief
```
Display IPv6 interface status
```plaintext
R1# show ipv6 interface brief
```
Display configuration of gigabitethernet 0/0/0 interface
```plaintext
R1# show running-config interface @iface@id
```
Display learned routes on the router
```plaintext
R1# show ip route
```
## Filtering the show command
TasksCommandsFilter by section
```plaintext
R1# show running-config | section line vty
line vty 0 4
 password 7 110A1016141D
 login
 transport input all
```
Filter by matches
```plaintext
R1# show ip interface brief | include up
GigabitEthernet0/0/0 192.168.10.1 YES manual up up
GigabitEthernet0/0/1 192.168.11.1 YES manual up up
Serial0/1/0 209.165.200.225 YES manual up up
```
Filter by exclusions
```plaintext
R1# show ip interface brief | exclude unassigned
Interface IP-Address OK? Method Status Protocol
GigabitEthernet0/0/0 192.168.10.1 YES manual up up
GigabitEthernet0/0/1 192.168.11.1 YES manual up up
Serial0/1/0 209.165.200.225 YES manual up up
```
Filter lines after match
```plaintext
R1# show ip route | begin Gateway
Gateway of last resort is not set
      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C 192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L 192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
```
# Router on a stick configuration:
## Router subinterface configuration:
TaskIOS CommandVLAN interface configuration
```plaintext
R1(config)# interface interface-id.vlan-id
R1(config-subif)# description Default Gateway for VLAN vlan-id
R1(config-subif)# encapsulation dot1Q vlan-id
R1(config-subif)# ip add @ip @mask
R1(config-subif)# exit
R1(config)#
R1(config)# interface interface-id2.vlan-id2
R1(config-subif)# description Default Gateway for VLAN vlan-id2
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add @ip2 @mask2
R1(config-subif)# exit
```
Management interface configuration
```plaintext
R1(config)# interface interface-id3.vlan-id3
R1(config-subif)# description Default Gateway for VLAN vlan-id3
R1(config-subif)# encapsulation dot1Q vlan-id3
R1(config-subif)# ip add @ip3 @mask3
R1(config-subif)# exit
```
Physical interface configuration
```plaintext
R1(config)# interface interface-id
R1(config-if)# description Trunk link to Switch
R1(config-if)# noshut
R1(config-if)# end
```
# DHCPv4 Server Configuration on a Cisco Router 
TaskIOS CommandExcluding IP addresses
```plaintext
Router(config)# ip dhcp excluded-address Low-add High-add
```
Defining the DHCP pool
```plaintext
Router(config)# ip dhcp pool pool-name
```
DHCP pool configuration
```plaintext
Router(dhcp-config)# network @res @mask
Router(dhcp-config)# default-router @ip_interface_router
Router(dhcp-config)# dns-server @ip
Router(dhcp-config)# domain-name site.com
```
Disabling DHCP service
```plaintext
Router(config)# no service dhcp
Router(config)# service dhcp
```
## DHCPv4 Configuration Verification
TaskIOS CommandDisplays commands related to the router's DHCPv4 configuration
```plaintext
Router# show running-config | section dhcp
```
Displays the list of MAC addresses to which the DHCP service provides an IP address
```plaintext
Router# show ip dhcp binding
```
Displays information about sent and received DHCPv4 messages
```plaintext
Router# show ip dhcp server statistics
```
## DHCPv4 Relay Configuration on a Cisco Router
TaskIOS CommandLAN interface of the network requiring DHCP service
```plaintext
Router(config)# interface @iface@id
```
DHCP server selection
```plaintext
Router(config-if)# ip helper-address @ip
```
## Configuring a router interface as a DHCP client
TaskIOS CommandRouter interface requiring DHCP service
```plaintext
Router(config)# interface @iface@id
```
Selecting IP address assignment via DHCP
```plaintext
Router(config-if)# ip address dhcp
```
## DHCPv6 Stateless Server Configuration
TaskIOS CommandEnable IPv6 unicast routing
```plaintext
R1(config)# ipv6 unicast-routing
```
Define the pool name
```plaintext
R1(config)# ipv6 dhcp pool IPV6-STATELESS
```
Pool configuration
```plaintext
R1(config-dhcpv6)# dns-server 2001:db8:acad:1::254
R1(config-dhcpv6)# domain-nameexample.com
```
LAN interface configuration
```plaintext
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# descriptionLink to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# no shut
```
## Configuring a DHCPv6 Stateless Client
TaskIOS CommandEnable IPv6 unicast routing
```plaintext
R1(config)# ipv6 unicast-routing
```
Interface configuration
```plaintext
R1(config)# interfaceg0/0/1
R1(config-if)# ipv6 enable
```
Enable DHCPv6 addressing
```plaintext
R1(config-if)# ipv6 address autoconfig
```
Verify IP address assignment
```plaintext
R1# show ipv6 interface brief
R1# show ipv6 dhcp interface g0/0/1
```
## Configuring a DHCPv6 Relay Agent
TaskIOS CommandChoose the interface for relay
```plaintext
R1(config)# interfaceg0/0/1
```
Interface configuration
```plaintext
Router(config-if)# ipv6 dhcp relay destination ipv6-address [interface-type interface-number]
```
Verify relay agent configuration
```plaintext
R1# show ipv6 interface dhcp
R1# show ipv6 dhcp binding
```
## DHCPv6 Stateful Server Configuration
TaskIOS CommandEnable IPv6 unicast routing
```plaintext
R1(config)# ipv6 unicast-routing
```
Define the pool name
```plaintext
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
```
Pool configuration
```plaintext
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server2001:4860:4860::8888
R1(config-dhcpv6)# domain-name  example.com
```
LAN interface configuration
```plaintext
R1(config)# interfaceGigabitEthernet0/0/1
R1(config-if)# descriptionLink to LAN
R1(config-if)# ipv6 addressfe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
```
## Configuring a DHCPv6 Stateful Client
TaskIOS CommandEnable IPv6 unicast routing
```plaintext
R1(config)# ipv6 unicast-routing
```
Interface configuration
```plaintext
R1(config)# interfaceg0/0/1
R1(config-if)# ipv6 enable
```
Enable DHCPv6 addressing
```plaintext
R1(config-if)# ipv6 address dhcp
```
Verify IP address assignment
```plaintext
R1# show ipv6 interface brief
R1# show ipv6 dhcp interface g0/0/1
```
## Verifying DHCPv6 Server Configuration
TaskIOS CommandVerify DHCPv6 pool name and parameters
```plaintext
R1# show ipv6 dhcp pool
```
Display client IPv6 link-local address and global unicast address assigned by server
```plaintext
R1# show ipv6 dhcp binding
```
# Routing
## Static Routing
TaskIOS Command
Static Route
Default Route
```plaintext
R1 (config)# ip route 10.0.0.0 255.0.0.0 10.1.1.1 5 instense
R1 (config)# ip route 0.0.0.0 0.0.0.0
```
## OSPF Dynamic Routing
TaskIOS Command
Enable OSPF under instance 1
Change OSPF router ID
Identify interfaces used + area
Share default gateway
No default OSPF messages
Reactivate OSPF messages
```plaintext
R1(config)# router ospf1
R1(config-ospf)# router-id1.1.1.1
R1(config-ospf)# network192.168.1.0 0.0.0.255 area 0
R1(config-ospf)# default-information originate
R1(config-ospf)# passive-interface default
R1(config-ospf)# no passive-interface s0/0/1
```
Redistribution of other routing protocols into OSPF
```plaintext
R1(config-ospf)# redistribute eigrp<id> subnets
R1(config-ospf)# redistribute bgp65234 subnets
R1(config-ospf)# redistribute protocol <id> subnets metric-type value
```
Default route
Route redistribution in OSPF
```plaintext
R1(config-if)# ip ospf priority 150
R1(config-if)# default-information originate
```
Virtual link
Summarization
```plaintext
R1config)#area 3 virtual-link 10.10.10.33
R1(config-ospf)# area<area_commune> virtual-link @ip_autre_routeur 
R1(config-ospf)# area<area-id> range <network> <mask> [advertise | not-advertise] 
```
OSPF parameters
```plaintext
R1(config)# interfaces0/0/1
R1(config-if)# ip ospf network point-to-point # only if serial interface for ipv4
R1(config-if)# ip ospf hello-interval 15
R1(config-if)# ip ospf dead-interval 60
R1(config-if)# bandwidth 64         #changes calculation metric, not effective BP
```
Enable OSPF authentication
Activation on interface
```plaintext
R1(config)# router ospf 1
R1(config-ospf)# interfaces0/0/1
R1(config-router)# area0 authentication message-digest
R1(config)# interfaces0/0/1
R1(config-if)# ip ospf authentication message-digest
R1(config-if)# ip ospf message-digest key 1 md5 <password>
```
## OSPFv3 Dynamic Routing for IPv6
TaskIOS CommandConfigure interface for OSPFv3
```plaintext
R1(config-ospf)# router-id 1.1.1.1
R1(config-ospf)# passive-interface default
R1(config-ospf)# no passive-interface s0/0/1
R1(config-if)# ipv6 ospf 1 area 0
R1(config-if)# ipv6 ospf network [point-to-point - broadcast – loopback – non-broadcast multiple access]
```
Redistribution of other routing protocols into OSPF
```plaintext
R1(config-ospf)# redistribute eigrp<id> subnets
R1(config-ospf)# redistribute bgp65234 subnets
R1(config-ospf)# redistribute protocol <id> subnets metric-type value
```
## EIGRP Dynamic Routing
TaskIOS Command
EIGRP Configuration
Parameters on the concerned interface
```plaintext
R1(config)# router eigrp 1
R1(config-eigrp)# network @ip_net @wild_card_mask
R1(config-eigrp)# passive-interface GigabitEthernet 0/0
R1(config-eigrp)# no auto-summary
R1(config-if)# ip hello-interval eigrp 1 10   # autonomous AS and time
R1(config-if)# ip hold-time eigrp 1 15
```
EIGRP route summarization
```plaintext
R2(config)#interface s0/0
R2(config-if)#ip summary-addresseigrp 1 192.168.1.96 255.255.255.224
```
## EIGRPv6 Dynamic Routing for IPv6
TaskIOS Command
Enable IPv6 routing
Assign port to EIGRP
```plaintext
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 router eigrp 1
R1(config)# router-id 1.1.1.1
R1(config)# interface G0/0
R1(config-router)# no shutdown
R1(config)#interface G0/0
R1(config)#ipv6 address 2001:db8:cafe:1::1/64
R1(config-if)#ipv6 address FE80::1 link-local
R1(config)# int g0/0
R1(config-if)# ipv6 eigrp 1
ipv6 route ::/0 2001:db8:a:2::1 5
redistribute static
```
Example of IPv6 route redistribution
```plaintext
R1(config)# ipv6 route ::/0 2001:db8:a:2::1 5
R1(config)# router eigrp 1
R1(config-eigrp)# redistribute static
```
## BGP Dynamic Routing
TaskIOS Command
Enable BGP AS 65200
Associate IPv4 with neighbor that has AS 65234
Announce network 10.0.0.0
```plaintext
R1(config)# router bgp 65200
R1(config-bgp)# bgp router-id 1.1.1.1
R1(config-bgp)# neighbor 192.168.1.23 remote-as 65234 
R1(config-bgp)# network 10.0.0.0 mask 255.0.0.0
```
# Address Translation Configuration: NAT (dynamic/static), PAT
## PAT
TaskIOS Command
ACL targeting network to translate
Allow addresses to be translated
```plaintext
R1(config)# access-list 1 permit192.168.1.0 0.0.0.255
R1(config)# ip nat inside source list 1interface s0/0/1 overload
```
External interface configuration
```plaintext
R1(config)# interface s0/0/1
R1(config)# ip nat outside
```
Internal interface configuration
```plaintext
R1(config)# interface gig0/1
R1(config)# ip nat inside
```
## Dynamic NAT
TaskIOS Command
ACL targeting network to translate
Allow addresses to be translated
```plaintext
R1(config)# ip access-list R1NAT
R1(config-nat)# permit 192.168.0.0 0.0.255.255
R1(config-nat)# ip nat pool R1POOL <ip_start> <ip_end> netmask 255.255.255.252
```
Link pool with ACL
```plaintext
R1(config)# ip nat inside source list R1NATpool R1POOL
```
## Static NAT
TaskIOS CommandStatic NAT assignment on source and destination
```plaintext
R1(config)# ip nat inside source static <ip_inside> <ip_outside>
```
# Access Lists
TaskIOS Command
Standard ACL (ID 1-99 / 1300-1999)
Filters sources only
apply to destinations (router+int+in/out)
```plaintext
R1(config)# access-list 1 permit192.168.0.1 0.0.255.255
R1(config-if)# ip access-group 1 out
R1(line-vty)# ip access-class 1 in
```
Extended ACL
(ID 100-199/2000-2699)
Filters sources, destinations, ports 
apply to source
(router +int+in/out)
```plaintext
R1(config)#access-list 100 permittcp 172.22.34.64 0.0.0.31 host 172.22.34.62 eq ftp
R1(config)# interface GigabitEthernet 0/0
R1(config-if)# ip access-group 100 in
```
Named ACL (if IOS 12.3 or later)
```plaintext
R1(config-if)# ip access-list extendedHTTP_ONLY
R1(config-ext-nacl)#permit tcp 172.22.34.96 0.0.0.15 host 172.22.34.62 eq www
```
IPv6 ACL
```plaintext
R1(config)# ipv6 access-list BLOCK_HTTP
R1(config-ipv6-acl)# ipv6 access-list BLOCK_HTTP
R1(config)# deny tcp any host 2001:DB8:1:30::30 eq www
R1(config)# interface f0/0
R1(config)# ipv6 traffic-filter BLOCK_HTTPin
```
# High Availability Configuration on Routers 
## HSRP, Configuration possible on L3 switches in SVI
TaskIOS Command
HSRP version 2 configuration
HSRP virtual IP assignment
Takes control if priority is higher
Track object 4 decrements priority by 60 if connectivity lost
```plaintext
R1(config-if)# standby version 2
R1(config-if)# standby 11 ip 10.0.100.254
R1(config-if)# standby 11 priority150
R1(config-if)# standby 11 preempt
R1(config-if)# standby 11 track4 decrement 60
```
HSRP configuration for IPv6
```plaintext
R1(config-if)# standby version 2
R1(config-if)# standby 12 ipv6 autoconfig
R1(config-if)# standby 12 priority150
R1(config-if)# standby 12 preempt
R1(config-if)# standby 12 track 6decrement 60
```
## VRRP: Configuration possible on L3 switches in SVI
TaskIOS Command
Select specific interface 
Configure virtual IP address
Configure virtual router priority
Configure advertisement interval
Display VRRP status on an interface
```plaintext
R1(config)# interface <interface>
R1(config-if)# vrrp <group-id> ip<virtual-ip-address>
R1(config-if)# vrrp <group-id> priority<priority>
R1(config-if)# vrrp <group-id> timers advertise <interval>
R1# show vrrp <interface>
```
Select specific interface 
Enable IPv6 on interface
Configure IPv6 address family
Configure virtual IP address
Configure virtual router priority
Configure advertisement interval
Display VRRPv3 status on an interface
```plaintext
R1(config)# interface <interface>
R1(config-if)# ipv6 enable
R1(config-if)# vrrpv3 <group-id>address-family ipv6
R1(config-if)# vrrpv3 <group-id>address <virtual-ipv6-address>
R1(config-if)# vrrpv3 <group-id>priority <priority>
R1(config-if)# vrrpv3 <group-id>timers advertise <interval>
R1# show vrrpv3 interface <interface>
```
# Various Other Configurations
## GRE Tunnel
TaskIOS CommandRouter 1 configuration
```plaintext
R1(config)# interface Tunnel0 
R1(config-if)# tunnel mode gre ip 
R1(config-if)# ip address 10.1.1.1 255.255.255.252 
R1(config-if)# tunnel source 209.165.201.1
R1(config-if)# tunnel destination 198.133.219.87  
R1(config)# router ospf 1
R1(config-router)# network 10.1.1.0 0.0.0.3 area 0
```
Router 2 configuration
```plaintext
R2(config)# interface Tunnel0 
R2(config-if)# tunnel mode gre ip 
R2(config-if)# ip address 10.1.1.2 255.255.255.252 
R2(config-if)# tunnel source 192.133.219.87  
R2(config-if)# tunnel destination 209.165.201.1  
R2(config)# router ospf 1 
R2(config-router)# network 10.1.1.0 0.0.0.3 area 0
```
## NTP Configuration (Time synchronized with a time server) 
TaskIOS Command
Change clock time
Set clock to current UTC time
Set R2 as NTP master with stratum 3
Update calendar
NTP server IP for time synchronization
Set log time to NTP time
```plaintext
R1# clock set 10:15:15 march 31 1993
R1(config)# clock timezone UTC 1
R1(config)# ntp master 3
R1(config)# ntp update-calendar
R1(config)# ntp server 2.2.2.2
R1# service timestamps log datetime local
```
## SYSLOG Configuration (Log capture by a remote server)
TaskIOS Command
Capture WARNING level syslogs
Send logs to specified IP
Enable logging
```plaintext
R1(config)# logging trap warning
R1(config)# logging host 10.0.100.5
R1(config)# logging on
```
## SNMP Agent Configuration (supervision client)
TaskIOS CommandACL allowing the server IP to make SNMP requests
```plaintext
R1(config)#ip access-list standard SNMP-NMS
R1(config-if)#permit host 10.0.100.5
```
Change SNMP contact value to a different name
Create SNMP Community STUDENT
Send STUDENT traps to PC1 with SNMP v2c
Persistent interface in case of reboot
Enable BGP traps
Enable config traps
Enable OSPF traps
```plaintext
R1(config)# snmp-server contact Cisco Student 
R1(config)# snmp-server community STUDENTro SNMP-NMS
R1(config)# snmp-server host 10.0.100.1version 2c STUDENT
R1(config)# snmp-server ifindex persist
R1(config)# snmp-server enable traps bgp 
R1(config)# snmp-server enable traps config
R1(config)# snmp-server enable traps ospf
```