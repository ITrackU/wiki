---
title: HP Procurve switching configurations commands
description: Easy setup commands to configure the hp switches
published: true
date: 2025-04-17T08:09:47.608Z
tags: switch, procurve, hp
editor: markdown
dateCreated: 2025-04-17T08:09:42.922Z
---

# Basic Configuration of the Management Interface

## Task

### ProCurve Commands

### Basic Configuration

```
enable
configure
hostname @hostname
ip domain-name @domain
```

### Interface Configuration

```
interface @interface
name "@description"
ip address @IP @MASK
no shutdown
```

### DHCP Configuration for LAN

```
ip helper-server @IP_DNS
dhcp-server @IP_DHCP_SERVER
dhcp-server @IP_DHCP_SERVER2
ip helper-address @IP_DHCP_SERVER
```

### Configure Default Routing on the Switch

```
ip route 0.0.0.0 0.0.0.0 @GATEWAY_IP
```

### Configure NTP on ProCurve

```
timesync ntp
ntp
ntp server @IP_NTP
ntp server @IP_NTP2
ntp authentication
ntp authentication-key @KEY_ID md5 @PASSWORD
```

### Configure SSH on ProCurve

```
crypto key generate ssh rsa bits 2048
ip ssh
ip ssh cipher aes256-ctr aes192-ctr aes128-ctr
ip ssh mac hmac-sha2-512 hmac-sha2-256 hmac-sha1
ip ssh filetransfer
```

### Configure User Authentication

```
aaa authentication local-user @username password @password
aaa authentication local-user @username group administrators
```

## VLAN Configuration

### Create VLANs

```
vlan @VLAN_ID
name "@VLAN_NAME"
untagged @PORT_LIST
ip address @IP @MASK
exit
```

### Access Port Configuration

```
interface @interface
untagged vlan @VLAN_ID
```

### Trunk Port Configuration

```
interface @interface
tagged vlan @VLAN_LIST
untagged vlan @NATIVE_VLAN_ID
```

### Delete VLAN

```
no vlan @VLAN_ID
```

### Show VLAN Configuration

```
show vlans
show vlan @VLAN_ID
```

## IP Routing Configuration

### Enable IP Routing

```
ip routing
```

### Configure Static Routes

```
ip route @DESTINATION_NETWORK @MASK @NEXT_HOP
```

### Enable RIP Routing

```
router rip
enable
network @NETWORK_ADDRESS
no auto-summary
```

### Enable OSPF Routing

```
router ospf
enable
area 0
network @NETWORK_ADDRESS @WILDCARD_MASK area 0
```

## ACL Configuration

### Create Standard ACL

```
ip access-list standard @ACL_NAME
permit @SOURCE_IP @WILDCARD_MASK
deny @SOURCE_IP @WILDCARD_MASK
exit
```

### Create Extended ACL

```
ip access-list extended @ACL_NAME
permit tcp @SOURCE_IP @SOURCE_WILDCARD @DESTINATION_IP @DESTINATION_WILDCARD eq @PORT
deny ip any any
exit
```

### Apply ACL to Interface

```
interface @interface
ip access-group @ACL_NAME in
ip access-group @ACL_NAME out
```

### Show ACL Configuration

```
show access-list
show access-list @ACL_NAME
show access-list config
```

## Spanning Tree Protocol Configuration

### Enable STP

```
spanning-tree
```

### Configure MSTP

```
spanning-tree mode mstp
spanning-tree config-name @CONFIG_NAME
spanning-tree config-revision @REVISION_NUMBER
```

### Configure RSTP

```
spanning-tree mode rapid-pvst
```

### Configure Root Bridge Priority

```
spanning-tree priority @PRIORITY
spanning-tree vlan @VLAN_ID priority @PRIORITY
```

### Enable STP Port Features

```
interface @interface
spanning-tree admin-edge-port
spanning-tree bpdu-protection
spanning-tree root-guard
```

### Show STP Configuration

```
show spanning-tree
show spanning-tree config
show spanning-tree instance @INSTANCE_ID
```

## Port Trunking / Link Aggregation (LACP)

### Create a Trunk Group

```
trunk @PORT_LIST trk1 lacp
```

### Configure Manual Trunk

```
trunk @PORT_LIST trk1 trunk
```

### Configure Trunk with LACP

```
interface @PORT_RANGE
lacp active
exit
trunk @PORT_LIST trk1 lacp
```

### Show Trunk Configuration

```
show trunks
show lacp
```

## Security Configuration

### Password Security

```
password manager user-name @USERNAME
password operator user-name @USERNAME
password all min-length @LENGTH
```

### Configure Local User Authentication

```
aaa authentication local-user @USERNAME group administrators password @PASSWORD
```

### Configure Console Authentication

```
aaa authentication console login local
aaa authentication console enable local
```

### Configure SSH Authentication

```
aaa authentication ssh login local
aaa authentication ssh enable local
```

### Enable Password Encryption

```
password all encrypt
```

### IP Source Guard

```
interface @interface
ip source-binding ip @IP mac @MAC vlan @VLAN_ID
```

### DHCP Snooping

```
dhcp-snooping
dhcp-snooping vlan @VLAN_ID
interface @interface
dhcp-snooping trust
```

### Dynamic ARP Protection

```
arp-protect
arp-protect vlan @VLAN_ID
interface @interface
arp-protect trust
```

## RADIUS Configuration

### Configure RADIUS Authentication

```
radius-server host @RADIUS_IP key @SECRET
aaa authentication login radius local
aaa authentication enable radius local
```

### Configure RADIUS Accounting

```
radius-server host @RADIUS_IP acct-port 1813 key @SECRET
aaa accounting commands radius
aaa accounting network start-stop radius
```

### Show RADIUS Configuration

```
show radius
show authentication
```

## 802.1X Configuration

### Enable 802.1X

```
aaa port-access authenticator @PORT_LIST
```

### Configure 802.1X Parameters

```
aaa port-access authenticator @PORT_LIST auth-vid @VLAN_ID
aaa port-access authenticator @PORT_LIST unauth-vid @VLAN_ID
aaa port-access authenticator @PORT_LIST client-limit @NUMBER
```

### Configure 802.1X Authentication Method

```
aaa authentication port-access eap-radius
aaa port-access authenticator @PORT_LIST
```

### Show 802.1X Configuration

```
show port-access authenticator
show port-access authenticator @PORT_LIST
show port-access authenticator config
```

## Quality of Service (QoS)

### Enable QoS

```
qos type-of-service diff-services
```

### Configure QoS Policy

```
qos classifier @NAME ip precedence @PRECEDENCE_VALUES
qos queue-config @QUEUE_NUMBER priority @PRIORITY
```

### Configure Interface QoS

```
interface @interface
qos priority @PRIORITY
qos dscp @DSCP_VALUE
qos trust dscp
```

### Show QoS Configuration

```
show qos
show qos interface @interface
```

## Port Mirroring

### Configure Local Port Mirroring

```
mirror @PORT_NUMBER port @SOURCE_PORT
```

### Configure Remote Port Mirroring

```
mirror remote ip @SOURCE_IP @DESTINATION_IP @SOURCE_UDP_PORT @DESTINATION_UDP_PORT
```

### Show Port Mirroring Configuration

```
show monitor
```

## IP Multicast Configuration

### Enable IGMP

```
ip igmp
vlan @VLAN_ID ip igmp
```

### Configure PIM

```
ip multicast-routing
router pim
enable
vlan @VLAN_ID ip pim-sparse
vlan @VLAN_ID ip pim-dense
```

### Show Multicast Configuration

```
show ip igmp config
show ip pim
```

## IPv6 Configuration

### Enable IPv6

```
ipv6 enable
ipv6 routing
```

### Configure IPv6 Interface

```
vlan @VLAN_ID
ipv6 address @IPv6_ADDRESS/@PREFIX_LENGTH
exit
```

### Configure IPv6 Static Route

```
ipv6 route @IPv6_PREFIX/@PREFIX_LENGTH @NEXT_HOP
```

### Show IPv6 Configuration

```
show ipv6
show ipv6 route
```

## SNMP Configuration

### Configure SNMP Community

```
snmp-server community @COMMUNITY_STRING operator
snmp-server community @COMMUNITY_STRING manager
```

### Configure SNMP Host

```
snmp-server host @IP_ADDRESS community @COMMUNITY_STRING
```

### Configure SNMPv3

```
snmp-server user @USERNAME auth md5 @AUTH_PASSWORD priv des @PRIV_PASSWORD
snmp-server group @GROUP_NAME user @USERNAME sec-model ver3
```

### Show SNMP Configuration

```
show snmp-server
show snmp-server community
```

## Stacking Configuration

### Enable Stacking

```
stacking enable
```

### Configure Stack Member

```
stacking member @MEMBER_ID type @SWITCH_TYPE
stacking member @MEMBER_ID priority @PRIORITY
```

### Show Stack Configuration

```
show stacking
```

## Firmware Management

### Copy Firmware to Flash

```
copy tftp flash @TFTP_SERVER @FILENAME primary
copy tftp flash @TFTP_SERVER @FILENAME secondary
```

### Boot from Specific Firmware

```
boot system flash primary
boot system flash secondary
```

### Show Firmware Information

```
show flash
show version
```

## Configuration Management

### Save Configuration

```
write memory
```

### Copy Configuration to TFTP

```
copy startup-config tftp @TFTP_SERVER @FILENAME
copy running-config tftp @TFTP_SERVER @FILENAME
```

### Copy Configuration from TFTP

```
copy tftp startup-config @TFTP_SERVER @FILENAME
copy tftp running-config @TFTP_SERVER @FILENAME
```

### Reset to Factory Default

```
erase startup-config
reload
```

### Show Configuration

```
show config
show running-config
show tech
```

## Debug Commands

### Debug Various Features

```
debug spanning-tree
debug lacp
debug arp
debug dhcp
debug ip routing
```

### Enable Syslog

```
logging @SYSLOG_SERVER
logging facility @FACILITY
logging severity @LEVEL
```

### Show Debug Information

```
show log
show debug
```
