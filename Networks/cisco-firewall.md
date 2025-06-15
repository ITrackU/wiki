---
title: Cisco ASA Firewalls
description: Configuration ASA Cisco Firewalls
published: true
date: 2025-04-16T12:29:12.768Z
tags: cisco, asa, firewall
editor: markdown
dateCreated: 2025-04-16T12:29:08.257Z
---


# Basic Interface Management Configuration

## Basic Configuration

```bash
enable
conf t
domain-name @domain
hostname @hostnm
```

## Interface Configuration

```bash
interface g1/X
nameif OUTSIDE/INSIDE/DMZ
security-level 1 /100/70
ip address @IP @MASK
no shutdown
```

## DHCP Configuration for LAN

```bash
dhcpd address @IP_LOW-@IP_HIGH INSIDE
dhcpd dns @IP_DNS interface INSIDE
dhcpd option 3 ip @IP_GATEWAY
dhcpd enable INSIDE
```

## Configure Default Routing on ASA

```bash
route OUTSIDE 0.0.0.0 0.0.0.0 @IP_PUB_REMOTE
```

## Configure NTP on ASA

```bash
ntp authenticate
ntp authentication-key @Nmb_key md5 @Pwd
ntp server @IP_NTP
ntp trusted-key @Nmb_key
```

## Configure AAA and SSH on ASA

```bash
username @user password @Pwd
aaa authentication ssh console LOCAL
crypto key generate rsa modulus 1024
yes
ssh @IP_ADMIN_STATION 255.255.255.255 INSIDE
ssh timeout 20
```

## Configure NAT for INSIDE and DMZ

```bash
conf t
object network @INSIDE-nat
subnet @IP_NETWORK_LAN @MASK_LAN
nat (inside,outside) dynamic/static interface @IP_PUB
```

## ACL Configuration

```bash
configure terminal
access-list NAT-IP-ALL extended permit ip any any
access-group NAT-IP-ALL in interface OUTSIDE
access-group NAT-IP-ALL in interface DMZ
access-list OUTSIDE-TO-DMZ extended permit tcp any host @IP_PUB_WEB eq @port
access-list OUTSIDE-TO-DMZ extended permit tcp host @IP_STATION_ADMIN host @IP_PUB_WEB eq @protocol
```

## IPSec VPN Between Two Routers

### ACL reused for Phase 2

```bash
access-list @ID_ACL permit ip Network_LAN_A @WC Network_LAN_B @WC
```

### Phase 1 ISAKMP Policy

```bash
crypto isakmp policy @ID
encryption aes 1024
hash sha
authentication pre-share
group 2
lifetime 1800

crypto isakmp key keypassword address @IP_PUB_B
```

### IPSec Policy

```bash
crypto ipsec transform-set VPN-SET esp-aes esp-sha-hmac
```

### Phase 2 Policy

```bash
crypto map VPN-MAP @ID ipsec-isakmp
match address @IP_ACL
set transform-set VPN-SET
set peer @IP_PUB_B
set pfs group2
set security-association lifetime seconds 1800
```

### Apply Crypto Map to Interface

```bash
int @int_PUB
crypto map VPN-MAP
```

## IPSec VPN Between ASA and Router

### ASA Configuration

#### ACL reused for Phase 2

```bash
access-list vpn extended permit ip 192.168.3.0 255.255.255.0 192.168.4.0 255.255.255.0
```

#### Phase 1 Policy

```bash
crypto isakmp policy 1
authentication pre-share
encryption 3des
hash md5
group 2
crypto isakmp key sharedkey address 192.168.2.2
tunnel-group 192.168.2.2 ipsec-attributes
pre-shared-key
crypto isakmp enable outside
```

#### Phase 2 Policy

```bash
crypto ipsec transform-set ts esp-3des esp-md5-hmac
crypto map vpn10 match address vpn
crypto map vpn10 set peer 192.168.2.2
crypto map vpn10 set transform-set ts
crypto map vpn interface outside
```

### Router Configuration

#### Phase 1 Policy

```bash
crypto isakmp policy 10
encr 3des
hash md5
authentication pre-share
group 2
crypto isakmp key sharedkey address 192.168.1.2
ip access-list extended vpn
permit ip 192.168.4.0 0.0.0.255 192.168.3.0 0.0.0.255
```

#### Phase 2 Policy

```bash
crypto ipsec transform-set ts esp-3des esp-md5-hmac
crypto map vpn 10 ipsec-isakmp
set peer 192.168.1.2
set transform-set ts
match address vpn
interface FastEthernet0/0
crypto map vpn
```

## VLAN Modification and Deletion

### Task: Remove VLAN assigned to an interface
```bash
Switch(config-if)# no switchport access vlan
```

### Task: Delete a VLAN
```bash
Switch# no vlan vlan-id
```

### Task: Delete all VLANs
```bash
Switch(config)# delete flash:vlan.dat
# Or
Switch(config)# delete vlan.dat
```

---

## Trunk Configuration

### Task: Enter global configuration mode
```bash
Switch# configure terminal
```

### Task: Enter interface configuration mode
```bash
Switch(config)# interface @interface_name @id
```

### Task: Set port to trunking mode permanently
```bash
Switch(config-if)# switchport mode trunk
```

### Task: Set a native VLAN other than VLAN 1
```bash
Switch(config-if)# switchport trunk native vlan vlan-id
```

### Task: Specify allowed VLANs on trunk link
```bash
Switch(config-if)# switchport trunk allowed vlan vlan-list
```

### Task: Return to privileged EXEC mode
```bash
Switch(config-if)# end
```

### Task: Verify trunk configuration
```bash
Switch# show interfaces interface-id switchport
```

### Task: Reset trunk to default state
```bash
Switch(config)# interface interface-id
Switch(config-if)# no switchport trunk allowed vlan
Switch(config-if)# no switchport trunk native vlan
```

---

## STP (Spanning Tree Protocol) Configuration

### Task: Set Rapid-PVST mode
```bash
Switch(config)# spanning-tree mode rapid-pvst
```

### Task: Configure primary and secondary STP roots
```bash
Switch1(config)# spanning-tree root vlan 1-1000 primary
Switch2(config)# spanning-tree root vlan 1-1000 secondary
```

### Task: Configure access ports
```bash
Switch(config)# interface @interface_name @ip
Switch(config-if)# spanning-tree portfast
```

### Task: Configure trunk ports
```bash
Switch(config)# interface range @iface@id - @iface@id
Switch(config-if-range)# spanning-tree guard root
Switch(config-if-range)# spanning-tree bpduguard enable
```

### Task: Configure link types
```bash
Switch(config-if-range)# spanning-tree link-type shared
# or
Switch(config-if-range)# spanning-tree link-type point-to-point
```

---

## EtherChannel Configuration

### Task: Configure LACP protocol
```bash
Switch1(config)# interface range @iface@id - @iface@id
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# switchport trunk native vlan X
Switch1(config-if)# channel-group 1 mode desirable

Switch2(config)# interface range @iface@id - @iface@id
Switch2(config-if)# switchport mode trunk
Switch2(config-if)# switchport trunk native vlan X
Switch2(config-if)# channel-group 1 mode auto
```

### Task: Configure PAGP protocol
```bash
Switch1(config)# interface range @iface@id - @iface@id
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# switchport trunk native vlan X
Switch1(config)# channel-group 1 mode active

Switch2(config)# interface range @iface@id - @iface@id
Switch2(config-if)# switchport mode trunk
Switch2(config-if)# switchport trunk native vlan X
Switch2(config)# channel-group 1 mode passive
```

### Task: Configure port-channel interface
```bash
Switch1(config)# interface port-channel 1
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# switchport trunk native vlan X

Switch2(config)# interface port-channel 1
Switch2(config-if)# switchport mode trunk
Switch2(config-if)# switchport trunk native vlan X
```

---

## Layer 3 Switch Inter-VLAN Routing

### Task: Create VLANs
```bash
Switch# configure terminal
Switch(config)# vlan vlan-id
Switch(config-vlan)# name vlan-name

Switch(config)# vlan vlan-id2
Switch(config-vlan)# name vlan-name2
```

### Task: Configure VLAN interfaces
```bash
Switch(config)# interface vlan vlan-id
Switch(config-if)# description default gateway SVI for @res/@mask
Switch(config-if)# ip add @ip @mask
Switch(config-if)# no shut

Switch(config)# interface vlan vlan-id2
Switch(config-if)# description default gateway svi for @res2/@mask2
Switch(config-if)# ip add @ip2 @mask2
Switch(config-if)# no shut
```

### Task: Configure access ports
```bash
Switch(config)# interface interface-id
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan vlan-id
Switch(config-if)# no shut

Switch(config)# interface interface-id2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan vlan-id2
Switch(config-if)# no shut
```

### Task: Enable IP routing
```bash
Switch(config)# ip routing
```

---

## Security Configuration: Password Encryption

### Task: Encrypt all passwords
```bash
Switch(config)# service password-encryption
```

### Task: Create random algorithm for password
```bash
Switch(config)# enable algorithm-type SCRYPT secret cisco12345cisco
```

### Task: Set secure hashed password
```bash
Switch(config)# enable secret password
```

### Task: Create local user with max rights and secure password
```bash
Switch(config)# username bob privilege 15 algorithm-type SCRYPT secret cisco12345cisco
```

### Task: Minimum password length
```bash
Switch(config)# security password min-length 10
```

### Task: Disable DNS lookup for unknown commands
```bash
Switch(config)# no ip domain-lookup
```

### Task: Lock console after failed login attempts
```bash
Switch(config)# login block for x attempts y within z
```

---

## RADIUS Client Configuration

### Task: Enable AAA model
```bash
Switch(config)# aaa new-model
```

### Task: Configure RADIUS server
```bash
Switch(config)# radius server RADIUS
Switch(config-radius)# address ipv4 X.X.X.X auth-port 1812 acct-port 1813
Switch(config-radius)# key password
```

### Task: Use local login for SSH instead of RADIUS
```bash
Switch(config)# aaa authentication login SSH-LOCAL local
```

---

## 802.1x Configuration

### Task: Enable 802.1x on an interface
```bash
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# authentication port-control auto
```

### Task: Configure guest VLAN
```bash
Switch(config)# dot1x guest-vlan supplicant
```

### Task: Verify 802.1x configuration
```bash
Switch# show authentication interface GigabitEthernet0/1
```

### Task: Create local user with secure password
```bash
Switch(config)# username bob privilege 15 algorithm-type SCRYPT secret cisco12345cisco
```
