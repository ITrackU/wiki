# Basic Management Interface Configuration

## Task
| IOS Commands |
|-------------|
| Enter global configuration mode. |
| `S1# configure terminal` |
| Enter interface configuration mode for SVI. |
| `S1(config)# interface vlan id` |
| Configure the IPv4 address of the management interface. |
| `S1(config-if)# ip address IP_ADDRESS MASK` |
| Configure the IPv6 address of the management interface. |
| `S1(config-if)# ipv6 address ipv6_address/mask` |
| Enable the management interface. |
| `S1(config-if)# no shutdown` |
| Return to privileged execution mode. |
| `S1(config-if)# end` |
| Save the running configuration to the startup configuration. |
| `S1# copy running-config startup-config` |

# Switch Port Configuration (Physical Layer)

## Task
| IOS Commands |
|-------------|
| Enter global configuration mode. |
| `S1# configure terminal` |
| Enter interface configuration mode. |
| `S1(config)# interface @iface@id` |
| Configure the interface duplex mode. |
| `S1(config-if)# duplex full` |
| Configure the interface speed. |
| `S1(config-if)# speed 100` |
| Return to privileged execution mode. |
| `S1(config-if)# end` |
| Configure auto MDIX. |
| `S1(config-if)# mdix auto` |
| Save the running configuration to the startup configuration. |
| `S1# copy running-config startup-config` |

# Switch Verification Commands

## Task
| IOS Commands |
|-------------|
| Display the status and configuration of interfaces. |
| `S1# show interfaces @iface@id` |
| Display the current startup configuration. |
| `S1# show startup-config` |
| Display the current running configuration. |
| `S1# show running-config` |
| Display Flash file system information. |
| `S1# show flash` |
| Display hardware and software system status. |
| `S1# show version` |
| Display command history. |
| `S1# show history` |
| Display IP information for an interface. |
| `S1# show ip interface @iface@id` |
| `S1# show ipv6 interface @iface@id` |
| Display the MAC address table. |
| `S1# show mac-address-table` |
| `S1# show mac address-table` |

# Remote Access via SSH

## Tasks
| Commands |
|-------------|
| Verify that the model is compatible with SSH. |
| `S1# show ip ssh` |
| Configure the IP domain (here cisco.com). |
| `S1(config)# ip domain-name cisco.com` |
| Generate RSA key pairs. |
| `S1(config)# crypto key generate rsa general-keys modulus 1024` |
| Configure user authentication. |
| `S1(config)# username bob secret password` |
| Configure VTY lines. |
| `S1(config)# line vty 0 15` |
| `S1(config-line)# transport input ssh` |
| `S1(config-line)# login local` |
| `S1(config-line)# exec-timeout 6` |
| `S1(config-line)# exit` |
| Improve SSH configuration. |
| `S1(config)# ip ssh time-out 60` |
| `S1(config)# ip ssh authentication-retries 2` |
| Enable SSH version 2. |
| `S1(config)# ip ssh version 2` |

# VLANs

## Creating a VLAN

| Task | IOS Command |
|------|-------------|
| Enter global configuration mode. | `Switch# configure terminal` |
| Create a VLAN with a valid ID number. | `Switch(config)# vlan vlan-id` |
| Specify a unique name to identify the VLAN. | `Switch(config-vlan)# name vlan-name` |

## Assigning a VLAN to a Port

| Task | IOS Command |
|------|-------------|
| Enter global configuration mode. | `Switch# configure terminal` |
| Enter interface configuration mode. | `Switch(config)# interface @interface_name @id` |
| Set the port to access mode. | `Switch(config-if)# switchport mode access` |
| Assign the port to a VLAN. | `Switch(config-if)# switchport access vlan vlan-id` |

## Verifying VLAN Information

| Task | Command Option |
|------|---------------|
| Display one line for each VLAN with the VLAN name, status, and ports. | `brief` |
| Display information about a VLAN identified by a VLAN ID. For vlan-id the range is 1 to 4094. | `id vlan-id` |
| Display information about a VLAN identified by a VLAN name. The vlan-name is an ASCII string of 1 to 32 characters. | `name vlan-name` |
| Display a summary of VLANs. | `summary` |

## VLAN Modifications and Deletion

| Task | Command Option |
|------|---------------|
| Remove the assigned VLAN from an interface. | `Switch(config-if)# no switchport access vlan` |
| Delete a VLAN. | `Switch# no vlan vlan-id` |
| Delete all VLANs. | `Switch(config)# delete flash:vlan.dat` or `Switch(config)# delete vlan.dat` |

## Trunk Configuration

| Task | IOS Command |
|------|-------------|
| Enter global configuration mode. | `Switch# configure terminal` |
| Enter interface configuration mode. | `Switch(config)# interface @interface_name @id` |
| Set the port to permanent trunking mode. | `Switch(config-if)# switchport mode trunk` |
| Choose a native VLAN other than VLAN 1. | `Switch(config-if)# switchport trunk native vlan vlan-id` |
| Specify the list of VLANs allowed on the trunk link. | `Switch(config-if)# switchport trunk allowed vlan vlan-list` |
| Return to privileged execution mode. | `Switch(config-if)# end` |
| Verify the trunk configuration. | `Switch# show interfaces interface-id switchport` |
| Reset the trunk to default state. | `Switch(config)# interface interface-id` |
| | `Switch(config-if)# no switchport trunk allowed vlan` |
| | `S1(config-if)# no switchport trunk native vlan` |

# STP Protocol Configuration

| Task | IOS Command |
|------|-------------|
| Configure Rapid-pvst mode. | `Switch(config)# spanning-tree mode rapid-pvst` |
| Configure primary and secondary spanning-tree root. | `Switch1(config)# spanning-tree root vlan 1-1000 primary` |
| | `Switch2(config)# spanning-tree root vlan 1-1000 secondary` |
| Configure access ports. | `Switch(config)# interface @interface_name @ip` |
| | `Switch(config-if)# spanning-tree portfast` |
| Configure trunk ports. | `Switch(config)# interface range @iface@id - @iface@id` |
| | `Switch(config-if-range)# spanning-tree guard root` |
| | `Switch(config-if-range)# spanning-tree bpduguard enable` |
| Configure shared link or point-to-point link. | `Switch(config-if-range)# spanning-tree link-type shared` |
| | or |
| | `Switch(config-if-range)# spanning-tree link-type point-to-point` |

# EtherChannel Configuration

## LACP Protocol Configuration
| Task | IOS Command |
|------|-------------|
| | `Switch1(config)# interface range @iface@id - @iface@id` |
| | `Switch1(config-if)# switchport mode trunk` |
| | `Switch1(config-if)# switchport trunk native vlan X` |
| | `Switch1(config-if)# channel-group 1 mode desirable` |
| | `Switch2(config)# interface range @iface@id - @iface@id` |
| | `Switch2(config-if)# switchport mode trunk` |
| | `Switch2(config-if)# switchport trunk native vlan X` |
| | `Switch2(config-if)# channel-group 1 mode auto` |

## PAGP Protocol Configuration
| Task | IOS Command |
|------|-------------|
| | `Switch1(config)# interface range @iface@id - @iface@id` |
| | `Switch1(config-if)# switchport mode trunk` |
| | `Switch1(config-if)# switchport trunk native vlan X` |
| | `Switch1(config)# channel-group 1 mode active` |
| | `Switch2(config)# interface range @iface@id - @iface@id` |
| | `Switch2(config-if)# switchport mode trunk` |
| | `Switch2(config-if)# switchport trunk native vlan X` |
| | `Switch2(config)# channel-group 1 mode passive` |

## Port Channel Configuration
| Task | IOS Command |
|------|-------------|
| | `Switch1(config)# interface port-channel 1` |
| | `Switch1(config-if)# switchport mode trunk` |
| | `Switch1(config-if)# switchport trunk native vlan X` |
| | `Switch2(config)# interface port-channel 1` |
| | `Switch2(config-if)# switchport mode trunk` |
| | `Switch1(config-if)# switchport trunk native vlan X` |

# Layer 3 Switch Inter-VLAN Routing Configuration

| Task | IOS Command |
|------|-------------|
| Creating VLANs | `Switch# configure terminal` |
| | `Switch(config)# vlan vlan-id` |
| | `Switch(config-vlan)# name vlan-name` |
| | `Switch(config)# vlan vlan-id2` |
| | `Switch(config-vlan)# name vlan-name2` |
| Configuring VLAN virtual interfaces | `Switch(config)# interface vlan vlan-id` |
| | `Switch(config-if)# description default gateway SVI for @res/@mask` |
| | `Switch(config-if)# ip add @ip @mask` |
| | `Switch(config-if)# no shut` |
| | `Switch(config-if)# exit` |
| | `Switch(config)#` |
| | `Switch(config)# int vlan vlan-id2` |
| | `Switch(config-if)# description default gateway svi for @res2/@mask2` |
| | `Switch(config-if)# ip add @ip2 @mask2` |
| | `Switch(config-if)# no shut` |
| | `Switch(config-if)# exit` |
| Configuring access ports | `Switch(config)# interface interface-id` |
| | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport access vlan vlan-id` |
| | `Switch(config-if)# no shut` |
| | `Switch(config)# interface interface-id2` |
| | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport access vlan vlan-id2` |
| | `Switch(config-if)# no shut` |
| Enable IP routing | `Switch(config)# ip routing` |

## Switchport Mode Configuration

| Option | Description |
|--------|-------------|
| access | Sets the interface (access port) to permanent non-trunking mode and negotiates to convert the link to a non-trunk link. The interface becomes a non-trunking interface, whether the neighboring interface is a trunk interface or not. |
| dynamic auto | Makes the interface able to convert the link into a trunk link. The interface becomes a trunk interface if the neighboring interface is set to trunk or desirable mode. The default switchport mode for all Ethernet interfaces is dynamic auto. |
| dynamic desirable | Makes the interface actively attempt to convert the link to a trunk link. The interface becomes a trunk interface if the neighboring interface is set to trunk, desirable, or dynamic auto mode. |
| trunk | Sets the interface to permanent trunk mode and negotiates to convert the neighboring link to a trunk link. The interface becomes a trunk interface even if the neighboring interface is not a trunk interface. |
| Verification of the configuration | `Switch# show dtp interface` |

# Router on a Stick Configuration (Layer 2)

## Switch Configuration

| Task | IOS Command |
|------|-------------|
| Create and name VLANs | `Switch# configure terminal` |
| | `Switch(config)# vlan vlan-id` |
| | `Switch(config-vlan)# name vlan-name` |
| | `Switch(config)# vlan vlan-id2` |
| | `Switch(config-vlan)# name vlan-name2` |
| Create the management interface | `Switch(config)# interface vlan vlan-id3` |
| | `Switch(config-if)# ip address @ip @mask` |
| | `Switch(config-if)# no shutdown` |
| | `Switch(config)# ip default-gateway @ip_interface_routeur` |
| Configure access ports (interfaces pointing to different VLANs) | `Switch(config)# interface interface-id` |
| | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport access vlan vlan-id` |
| | `Switch(config-if)# no shut` |
| | `Switch(config)# interface interface-id2` |
| | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport access vlan vlan-id2` |
| | `Switch(config-if)# no shut` |
| Configure trunk ports (interface pointing to the router) | `S1(config)# interface interface-id3` |
| | `S1(config-if)# switchport mode trunk` |
| | `S1(config-if)# no shut` |

# Security Configuration: Password Encryption

| Task | IOS Command |
|------|-------------|
| Encrypt all passwords | `Switch(config)# service password-encryption` |
| Create a random algorithm | `Switch(config)# enable algorithm-type SCRYPT secret cisco12345cisco` |
| Enable secure and hashed password | `Switch(config)# enable secret password` |
| Create a local user with maximum privileges and a secure password | `Switch(config)# username bob privilege 15 algorithm-type SCRYPT secret cisco12345cisco` |
| Encrypt all clear text passwords | `Switch(config)# service password encryption` |
| Minimum number of characters | `Switch(config)# security password min-length 10` |
| Disable DNS lookup for unknown commands | `Switch(config)# no ip domain-lookup` |
| Block for x seconds if y attempts in less than z seconds | `Switch(config)# login block for x attempts y within z` |

# RADIUS Client Configuration

| Task | IOS Command |
|------|-------------|
| Enable the Authentication Authorization and Accounting security model | `Switch(config)# aaa new-model` |
| Configure the RADIUS server | `Switch(config)# radius server RADIUS` |
| | `Switch(config-radius)# address ipv4 X.X.X.X auth-port 1812 acct-port 1813` |
| | `Switch(config-radius)# key password` |
| Detach SSH authentication from the RADIUS server, and set it to local | `Switch(config)# aaa authentication login SSH-LOCAL local` |
| Create a local user with maximum privileges and a secure password | `Switch(config)# username bob privilege 15 algorithm-type SCRYPT secret cisco12345cisco` |

# 802.1x Configuration

| Task | IOS Command |
|------|-------------|
| Configure 802.1x on the interface | `Switch(config)# interface GigabitEthernet 0/1` |
| | `Switch(config-if)# authentication port-control auto` |
| Configure the guest-VLAN | `Switch(config)# dot1x guest-vlan supplicant` |
| Verify 802.1x configuration | `Switch(config)# show authentication interface GigabitEthernet 0/1` |
| Create a local user with maximum privileges and a secure password | `Switch(config)# username bob privilege 15 algorithm-type SCRYPT secret cisco12345cisco` |

# Port Security

| Task | IOS Command |
|------|-------------|
| Disable unused ports | `Switch(config)# interface range fA/B` |
| Trunk negotiation disabled | `Switch(config-if)# shutdown` |
| | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport nonegotiate` |
| Port Security enabled on the interface | `Switch(config)# interface range fX/Y` |
| 2 MAC addresses allowed on the port | `Switch(config-if)# switchport mode access` |
| | `Switch(config-if)# switchport port-security` |
| | `Switch(config-if)# switchport port-security maximum 2` |
| | `Switch(config-if)# switchport port-security mac-address sticky` |
| | `Switch(config-if)# switchport port-security violation restrict` |
| | `Switch(config-if)# switchport nonegotiate` |
| STP Security | `Switch(config)# interface range fX/Y` |
| | `Switch(config-if)# spanning-tree bpduguard enable` |
| | `Switch(config-if)# spanning-tree portfast` |