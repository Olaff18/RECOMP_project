# Sprint 1

![](sites/all.jpg)

## M1B - Team 3

| Name                     | Student Number | Role / Site Responsibility |
| ------------------------ | -------------- | -------------------------- |
| Nuno Cunha               | 1211689        | *Oporto (HQ)*            |
| *João Fernandes*      | *1211681*    | *Warsaw (BR1)*           |
| *Michal Binek*         | *1260224*    | *Munich (BR2)*           |
| *Francisco Magalhães* | *1170589*    | *The Vault*              |

## General Configuration

- All unused switch ports are assigned to VLAN 99 and administratively shutdown;

---

## 1. Oporto (HQ)

![](sites/oporto.jpg)

### <u></u> 10.42.56.0/22

### 1.1 Network Addressing Table

| Network              | Hosts req. | CIDR | Mask            | Network Address | Broadcast Address | First Host   | Last Host    |
| -------------------- | ---------- | ---- | --------------- | --------------- | ----------------- | ------------ | ------------ |
| VLAN40 – USERS      | 500        | /23  | 255.255.254.0   | 10.42.56.0      | 10.42.57.255      | 10.42.56.1   | 10.42.57.254 |
| VLAN20 – ACCOUNTING | 200        | /24  | 255.255.255.0   | 10.42.58.0      | 10.42.58.255      | 10.42.58.1   | 10.42.58.254 |
| VLAN30 – HR         | 100        | /25  | 255.255.255.128 | 10.42.59.0      | 10.42.59.127      | 10.42.59.1   | 10.42.59.126 |
| VLAN10 – STAFF      | 50         | /26  | 255.255.255.192 | 10.42.59.128    | 10.42.59.191      | 10.42.59.129 | 10.42.59.190 |
| HQ ↔ MLS1           | 2          | /30  | 255.255.255.252 | 10.42.59.192    | 10.42.59.195      | 10.42.59.193 | 10.42.59.194 |
| HQ ↔ MLS2           | 2          | /30  | 255.255.255.252 | 10.42.59.196    | 10.42.59.199      | 10.42.59.197 | 10.42.59.198 |

### 1.2 Interface Addressing Table

#### 1.2.1 HQ (Oporto)

| Interface | Assigned IP   | Subnet Mask | Purpose                  |
| --------- | ------------- | ----------- | ------------------------ |
| Gig0/0    | 10.42.59.193  | /30         | Link to MLS1             |
| Gig0/1    | 10.42.59.197  | /30         | Link to MLS2             |
| Gig0/0/0  | DHCP-assigned | /27         | Internet (SP) connection |

#### 1.2.2 MLS1

| Interface     | Assigned IP  | Subnet Mask | Purpose                                    |
| ------------- | ------------ | ----------- | ------------------------------------------ |
| Gig0/1        | 10.42.59.194 | /30         | Link to HQ (Oporto)                        |
| Gig0/2        | —           | —          | Unused (VLAN 99, shutdown)                 |
| Port-channel1 | —           | —          | Trunk to MLS2 (LACP)                       |
| Fa0/1         | —           | —          | Po1 member – to MLS2                      |
| Fa0/2         | —           | —          | Po1 member – to MLS2                      |
| Fa0/3         | —           | —          | Trunk to SW1                               |
| Fa0/4         | —           | —          | Trunk to SW2                               |
| Fa0/5–24     | —           | —          | Unused (VLAN 99, shutdown)                 |
| VLAN10        | 10.42.59.129 | /26         | STAFF SVI (HSRP VIP .190, active)          |
| VLAN20        | 10.42.58.1   | /24         | ACCOUNTING SVI (HSRP VIP .254, active)     |
| VLAN30        | 10.42.59.1   | /25         | HR SVI (HSRP VIP .126, standby)            |
| VLAN40        | 10.42.56.1   | /23         | USERS SVI (HSRP VIP 10.42.57.254, standby) |

#### 1.2.3 MLS2

| Interface     | Assigned IP  | Subnet Mask | Purpose                                   |
| ------------- | ------------ | ----------- | ----------------------------------------- |
| Gig0/1        | 10.42.59.198 | /30         | Link to HQ (Oporto)                       |
| Gig0/2        | —           | —          | Unused (VLAN 99, shutdown)                |
| Port-channel1 | —           | —          | Trunk to MLS1 (LACP)                      |
| Fa0/1         | —           | —          | Po1 member – to MLS1                     |
| Fa0/2         | —           | —          | Po1 member – to MLS1                     |
| Fa0/3         | —           | —          | Trunk to SW1                              |
| Fa0/4         | —           | —          | Trunk to SW2                              |
| Fa0/5–24     | —           | —          | Unused (VLAN 99, shutdown)                |
| VLAN10        | 10.42.59.130 | /26         | STAFF SVI (HSRP VIP .190, standby)        |
| VLAN20        | 10.42.58.2   | /24         | ACCOUNTING SVI (HSRP VIP .254, standby)   |
| VLAN30        | 10.42.59.2   | /25         | HR SVI (HSRP VIP .126, active)            |
| VLAN40        | 10.42.56.2   | /23         | USERS SVI (HSRP VIP 10.42.57.254, active) |

#### 1.2.4 SW1

| Interface             | Purpose                      |
| --------------------- | ---------------------------- |
| Fa0/1–2              | Unused (VLAN 99, shutdown)   |
| Fa0/3                 | Trunk to MLS1                |
| Fa0/4                 | Trunk to MLS2                |
| Fa0/5–8              | Access – VLAN 10 STAFF      |
| Fa0/9–12             | Access – VLAN 20 ACCOUNTING |
| Fa0/13–16            | Access – VLAN 30 HR         |
| Fa0/17–20            | Access – VLAN 40 USERS      |
| Fa0/21–24, Gig0/1–2 | Unused (VLAN 99, shutdown)   |

#### 1.2.5 SW2

| Interface             | Purpose                      |
| --------------------- | ---------------------------- |
| Fa0/1–2              | Unused (VLAN 99, shutdown)   |
| Fa0/3                 | Trunk to MLS1                |
| Fa0/4                 | Trunk to MLS2                |
| Fa0/5–8              | Access – VLAN 10 STAFF      |
| Fa0/9–12             | Access – VLAN 20 ACCOUNTING |
| Fa0/13–16            | Access – VLAN 30 HR         |
| Fa0/17–20            | Access – VLAN 40 USERS      |
| Fa0/21–24, Gig0/1–2 | Unused (VLAN 99, shutdown)   |

#### 1.2.6 Oporto-STAFF

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /26         | VLAN10 host |

#### 1.2.7 Oporto-ACCOUNTING

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /24         | VLAN20 host |

#### 1.2.8 Oporto-HR

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /25         | VLAN30 host |

#### 1.2.9 Oporto-USERS

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /23         | VLAN40 host |

### 1.3 Connectivity Tests

| Source        | Destination        | Test Type | Result  |
| ------------- | ------------------ | --------- | ------- |
| HQ Router     | SP gateway         | `ping`  | Success |
| HQ PC (STAFF) | HQ PC (ACCOUNTING) | `ping`  | Success |
| HQ PC (STAFF) | HQ PC (HR)         | `ping`  | Success |
| HQ PC (STAFF) | HQ PC (USERS)      | `ping`  | Success |

### 1.4 Implementation & Testing

#### 1.4.1 VLAN's configuration and connections

First we started by configuring the trunk serving as the link between the MLS switches, with the following configuration in both switches:

    interface PortChannel 1
     description InterSwitch Link - Aggregated Link
     switchport nonegotiate
     switchport trunk encapsulation dot1q
     switchport mode trunk
     switchport trunk allowed vlan 10,20,30,40
     switchport trunk native vlan 50
     no shutdown
    !
    interface range FastEthernet0/1-2
     description InterSwitch Link
     channel-group 1 mode active
    !

From top to bottom, we did executed the following:

- Created a *PortChannel* interface which holds all the configurations for the link
- Inside the interface configuration mode, we:
  - Disabled DTP with the command *switchport nonegotiate*, mitigating possible VLAN hopping attacks.
  - Enabled the *802.1Q* encapsulation on the interface.
  - Set the interface mode to *trunk* and specified the allowed vlans, as well as the native vlan, for untagged traffic going through this interface.
- Configured the interfaces *FastEthernet0/1* and *FastEthernet0/2* to join the previously created *PortChannel* interface using *LACP*, because it is a *non-proprietary* protocol, which gives us the possibility to add equipment from another vendor, in the future.

For verification purposes we executed the following commands:

- *show etherchannel summary* to have an overview of all the etherchannels configured.
- *show interface trunk* to verify if the interface is really in trunk mode and what VLANs are allowed.

![](prints/Oporto/ISL-Oporto.png)

After that, the *VTP* domain was created, with the following configuration snippet:

    vtp mode server
    vtp domain RECOMP2627M1B03
    vtp password RECOMP_2026#
    !
    vlan 10
     name STAFF
    !
    vlan 20
     name ACCOUNTING
    !
    vlan 30
     name HR
    !
    vlan 40
     name USERS
    !
    vlan 50
     name NATIVE
    !
    vlan 99
     name BLACKHOLE
    !

In this section, we did the following:

- Set the switch to *VTP server* mode, so it can add, edit and remove VLANs and share these changes with the other switches in the domain.
- Joined the *RECOMP2627M1B03* VTP domain, since switches only exchange VLAN information with others in the same domain.
- Added a VTP password, so only switches with the same password can join the domain, preventing rogue devices from changing the VLAN database.
- Created and named the VLANs. These are only created on this switch, as VTP takes care of propagating them to the rest of the network.

*MLS2* is also a *VTP server* in the same domain, acting as a backup. If *MLS1* fails, VLANs can still be managed from *MLS2*. *SW1* and *SW2* are *VTP clients*, so they learn the VLANs automatically from the servers.

To know which VLAN information is the newest, VTP uses a *configuration revision number*. Each change on a server increases it by one, and switches only accept updates with a higher number than their own.

To verify that VTP was correctly implemented, two tests were performed:

- We executed the command *show vtp status* on *MLS1*, the Primary VTP server, to check its current VTP role:

![](prints/Oporto/VTP-MLS1.png)

As shown, the device is operating as a server in the correct VTP domain.

- On one of the Layer 2 switches, we ran the same command to confirm that its configuration revision number matches the one on the server. We then tried to create a VLAN, expecting the operation to fail.

![](prints/Oporto/VTP-L2.png)

The output matches the expected behaviour, confirming that VTP was properly configured.

#### 1.4.2 Rapid Spanning Tree Protocol

To configure Rapid-PVST+ and the root bridge roles, we did the following:

- Changed the spanning tree mode to *Rapid-PVST+* on all switches.
- On *MLS1*, made it the root bridge for VLANs 10 and 20 with the *root primary* command, and the backup root for VLANs 30 and 40 with *root secondary*.
- On *MLS2*, applied the opposite configuration, making it the root bridge for VLANs 30 and 40 and the backup root for VLANs 10 and 20.
- On *SW1* and *SW2*, only set the spanning tree mode, keeping the default priority (32768) so they never compete with the MLSs in the root election.

As we can see in the next samples of config:

- HQ_MLS1

```
spanning-tree mode rapid-pvst
!
spanning-tree vlan 10 root primary
spanning-tree vlan 20 root primary
spanning-tree vlan 30 root secondary
spanning-tree vlan 40 root secondary
```

- HQ_MLS2

```
spanning-tree mode rapid-pvst
!
spanning-tree vlan 10 root secondary
spanning-tree vlan 20 root secondary
spanning-tree vlan 30 root primary
spanning-tree vlan 40 root primary
```

- HQ_SW01 / HQ_SW02

```
spanning-tree mode rapid-pvst
```

To confirm that the configuration was correctly applied, three tests were carried out:

- On *MLS1*, we ran the command *show spanning-tree vlan 10* to check whether it had been elected as the root bridge for this VLAN:

![](prints/Oporto/STP-MLS1.png)

The output shows the message *This bridge is the root*, along with a lowered bridge priority, confirming that *MLS1* is the root bridge for VLAN 10 and by consequence of VLAN 20 as well.

- On *MLS2*, we ran *show spanning-tree vlan 30* to make sure the opposite roles were in place:

![](prints/Oporto/STP-MLS2.png)

Once again, the device is identified as the root bridge, this time for VLAN's 30 and 40, as intended.

- Finally, on one of the Layer 2 switches, we used *show spanning-tree summary* to verify the spanning tree mode in use:

![](prints/Oporto/STP-L2.png)

The switch is running in *rapid-pvst* mode, showing that the configuration was applied across the network.

With these results, we can conclude that Rapid-PVST+ is working as planned, with the root bridge roles correctly split between both MLSs, providing load balancing and redundancy.

#### 1.4.3 Configure HSRP

To configure the SVIs, HSRP and the routed ports, we did the following:

- Enabled IP routing on both MLSs with the command *ip routing*, allowing them to route traffic between VLANs and towards the rest of the network.
- Created an SVI for each user VLAN (10, 20, 30 and 40) on both MLSs and assigned them the addresses defined in the addressing scheme. *MLS1* uses the first usable address of each subnet and *MLS2* the second.
- Configured HSRP on each SVI:
  - Set *HSRP version 2*, which supports a larger range of group numbers, allowing each group to match its VLAN ID.
  - Defined a virtual IP for each group, using the last usable address of the subnet. This is the address the hosts use as their default gateway, and it is shared by both MLSs.
  - Set a priority of 150 (the default being 100) on the MLS that is the STP root bridge for that VLAN, making it the active router: *MLS1* for VLANs 10 and 20, and *MLS2* for VLANs 30 and 40.
  - Enabled *preempt* on all groups, so that when the preferred MLS recovers from a failure, it takes back the active role.
- Converted *GigabitEthernet0/1* into a routed port with the command *no switchport* and assigned it an IP address from a /30 subnet, creating a point-to-point link from each MLS towards the HQ.

Matching the HSRP active router with the STP root bridge ensures that traffic from each VLAN reaches its gateway through the most direct path, without crossing the link between the two MLSs unnecessarily. If one MLS fails, the other takes over both the gateway and root roles, keeping the network available.

- HQ_MLS1

```
ip routing
!
interface Vlan10
 description STAFF
 ip address 10.42.59.129 255.255.255.192
 standby version 2
 standby 10 ip 10.42.59.190
 standby 10 priority 150
 standby 10 preempt
 no shutdown
!
interface Vlan20
 description ACCOUNTING
 ip address 10.42.58.1 255.255.255.0
 standby version 2
 standby 20 ip 10.42.58.254
 standby 20 priority 150
 standby 20 preempt
 no shutdown
!
interface Vlan30
 description HR
 ip address 10.42.59.1 255.255.255.128
 standby version 2
 standby 30 ip 10.42.59.126
 standby 30 preempt
 no shutdown
!
interface Vlan40
 description USERS
 ip address 10.42.56.1 255.255.254.0
 standby version 2
 standby 40 ip 10.42.57.254
 standby 40 preempt
 no shutdown
!
interface GigabitEthernet0/1
 no switchport
 ip address 10.42.59.194 255.255.255.252
 no shutdown
```

- HQ_MLS2

```
ip routing
!
interface Vlan10
 description STAFF
 ip address 10.42.59.130 255.255.255.192
 standby version 2
 standby 10 ip 10.42.59.190
 standby 10 preempt
 no shutdown
!
interface Vlan20
 description ACCOUNTING
 ip address 10.42.58.2 255.255.255.0
 standby version 2
 standby 20 ip 10.42.58.254
 standby 20 preempt
 no shutdown
!
interface Vlan30
 description HR
 ip address 10.42.59.2 255.255.255.128
 standby version 2
 standby 30 ip 10.42.59.126
 standby 30 priority 150
 standby 30 preempt
 no shutdown
!
interface Vlan40
 description USERS
 ip address 10.42.56.2 255.255.254.0
 standby version 2
 standby 40 ip 10.42.57.254
 standby 40 priority 150
 standby 40 preempt
 no shutdown
!
interface GigabitEthernet0/1
 no switchport
 ip address 10.42.59.198 255.255.255.252
 no shutdown
```

To confirm that the configuration was correctly applied, the following tests were carried out:

- On *MLS1*, we ran the command *show standby brief* to check the HSRP state of each group:

![](prints/Oporto/HSRP-MLS1.png)

*MLS1* is shown as *Active* for groups 10 and 20 and as *Standby* for groups 30 and 40, matching its STP root roles.

- On *MLS2*, we ran the same command to confirm the opposite roles:

![](prints/Oporto/HSRP-MLS2.png)

As expected, *MLS2* is *Active* for groups 30 and 40 and *Standby* for groups 10 and 20.

- To test the redundancy, we shut down the *VLAN 10* SVI on *MLS1* and ran the command *show standby brief* on *MLS2*, expecting it to take over the active role for this VLAN:

![](prints/Oporto/HSRP-Failover.png)

- Finally, we ran *show ip interface brief* on both MLSs to verify the routed ports:

  - MLS1

  ![](prints/Oporto/Routed-MLS1.png)

  - MLS2

  ![](prints/Oporto/Routed-MLS2.png)

#### 1.4.4 Configure Layer 2 Switch

To configure the ports on both Layer 2 switches, we did the following:

- Grouped the access ports by department using the *interface range* command and assigned each group to its VLAN:
  - *FastEthernet0/5–8* to *VLAN 10 (STAFF)*;
  - *FastEthernet0/9–12* to *VLAN 20 (ACCOUNTING)*;
  - *FastEthernet0/13–16* to *VLAN 30 (HR)*;
  - *FastEthernet0/17–20* to *VLAN 40 (USERS)*.
- Set these ports to *access* mode, so they only carry traffic from a single VLAN and never form a trunk.
- Enabled port security on every access port:
  - Limited each port to a *maximum of 1* MAC address, so only one device can be connected at a time.
  - Used *sticky* learning, which makes the switch automatically learn the MAC address of the first connected device and save it to the running configuration.
  - Set the violation mode to *shutdown*, putting the port in an *err-disabled* state if an unknown device is connected.
- Enabled *PortFast*, allowing the ports to skip the spanning tree listening and learning stages and come up immediately, and *BPDU Guard*, which shuts the port down if it receives a BPDU, preventing unauthorized switches from being plugged in.
- Configured the uplink ports *FastEthernet0/3* and *FastEthernet0/4*, which connect to *MLS1* and *MLS2*, as trunks and marked them as *trusted* for both *DHCP Snooping* and *Dynamic ARP Inspection*. Since DHCP Snooping and DAI are enabled for VLANs 10 to 40, all other ports are untrusted by default, meaning that only the uplinks, where the legitimate DHCP servers are located, can forward DHCP offers. This prevents rogue DHCP servers and ARP spoofing attacks from the access ports.
- Assigned all remaining ports (*FastEthernet0/1–2*, *FastEthernet0/21–24* and *GigabitEthernet0/1–2*) to *VLAN 99 (BLACKHOLE)* and shut them down, so a device plugged into them has no network access.

The same configuration was applied to both *SW1* and *SW2*.

```
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,40
!
ip arp inspection vlan 10,20,30,40
!
no ip dhcp snooping information option
!
interface FastEthernet0/3
 description Uplink to HQ-MLS01
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 10,20,30,40
 switchport trunk native vlan 50
 ip dhcp snooping trust
 ip arp inspection trust
 no shutdown
!
interface FastEthernet0/4
 description Uplink to HQ-MLS02
 switchport mode trunk
 switchport nonegotiate
 switchport trunk allowed vlan 10,20,30,40
 switchport trunk native vlan 50
 ip dhcp snooping trust
 ip arp inspection trust
 no shutdown
!
interface range FastEthernet0/5-8
 description ACCESS PORTS - STAFF
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
!
interface range FastEthernet0/9-12
 description ACCESS PORTS - ACCOUNTING
 switchport mode access
 switchport access vlan 20
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
!
interface range FastEthernet0/13-16
 description ACCESS PORTS - HR
 switchport mode access
 switchport access vlan 30
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
!
interface range FastEthernet0/17-20
 description ACCESS PORTS - USERS
 switchport mode access
 switchport access vlan 40
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
!
interface range FastEthernet0/1-2, FastEthernet0/21-24, GigabitEthernet0/1-2
 description NOT IN USE
 switchport mode access
 switchport access vlan 99
 shutdown
```

#### 1.4.5 Configure DHCP

To configure DHCP, we did the following on both *MLS1* and *MLS2*:

- Excluded from the DHCP pools all addresses that are statically assigned, so they are never handed out to hosts:
  - The SVI addresses of *MLS1* and *MLS2* in each VLAN (the first two usable addresses of each subnet);
  - The HSRP virtual IP of each VLAN (the last usable address of each subnet).
- Created a DHCP pool for each user VLAN (*STAFF_POOL*, *ACCOUNTING_POOL*, *HR_POOL* and *USERS_POOL*) and, inside each one:
  - Defined the *network* according to the addressing scheme;
  - Set the *default-router* to the HSRP virtual IP of the VLAN, instead of the real address of one of the MLSs. This way, the hosts keep the same gateway even if one of the switches fails;
  - Set the *DNS server* to 8.8.8.8;
  - Set the *domain name* to *RECOMP2627M1B03.recomp.com*.

Since both MLSs have the same pools, either of them can answer DHCP requests, so hosts can still receive an address if one of the switches goes down. To avoid giving the same address to two hosts, the Cisco DHCP server sends a ping to an address before offering it, and skips it if it gets a reply.

```
ip dhcp excluded-address 10.42.59.129 10.42.59.130
ip dhcp excluded-address 10.42.59.190
!
ip dhcp pool STAFF_POOL
 network 10.42.59.128 255.255.255.192
 default-router 10.42.59.190
 dns-server 8.8.8.8
 domain-name RECOMP2627M1B03.recomp.com
!
ip dhcp excluded-address 10.42.58.1 10.42.58.2
ip dhcp excluded-address 10.42.58.254
!
ip dhcp pool ACCOUNTING_POOL
 network 10.42.58.0 255.255.255.0
 default-router 10.42.58.254
 dns-server 8.8.8.8
 domain-name RECOMP2627M1B03.recomp.com
!
ip dhcp excluded-address 10.42.59.1 10.42.59.2
ip dhcp excluded-address 10.42.59.126
!
ip dhcp pool HR_POOL
 network 10.42.59.0 255.255.255.128
 default-router 10.42.59.126
 dns-server 8.8.8.8
 domain-name RECOMP2627M1B03.recomp.com
!
ip dhcp excluded-address 10.42.56.1 10.42.56.2
ip dhcp excluded-address 10.42.57.254
!
ip dhcp pool USERS_POOL
 network 10.42.56.0 255.255.254.0
 default-router 10.42.57.254
 dns-server 8.8.8.8
 domain-name RECOMP2627M1B03.recomp.com
```

To confirm that DHCP was working correctly, the command *show ip dhcp bindings* was executed to see the DHCP leases

![](prints/Oporto/DHCP-Leases.png)

As presented in the previous image, the DHCP is working as expected.

#### 1.4.6 Connectivity Tests

- STAFF to ACCOUNTING

![](prints/Oporto/STAFF-ACC.png)

- STAFF to HR

![](prints/Oporto/STAFF-HR.png)

- STAFF to USERS

![](prints/Oporto/STAFF-USERS.png)

- ACCOUNTING to HR

![](prints/Oporto/ACC-HR.png)

- ACCOUNTING to USERS

![](prints/Oporto/ACC-USERS.png)

- HR to USERS

![](prints/Oporto/HR-USERS.png)

As is presented, connectivity between all networks was achieved.

---

## 2. Warsaw (BR1)

![](sites/warsaw.jpg)

### <u></u> 172.23.68.0/23

### 2.1 Network Addressing Table

| Network              | Hosts req. | CIDR | Mask            | Network Address | Broadcast Address | First Host   | Last Host     |
| -------------------- | ---------- | ---- | --------------- | --------------- | ----------------- | ------------ | ------------- |
| VLAN40 – USERS      | 200        | /24  | 255.255.255.0   | 172.23.68.0     | 172.23.68.255     | 172.23.68.1  | 172.23.68.254 |
| VLAN10 – STAFF      | 30         | /27  | 255.255.255.224 | 172.23.69.0     | 172.23.69.31      | 172.23.69.1  | 172.23.69.30  |
| VLAN20 – ACCOUNTING | 20         | /27  | 255.255.255.224 | 172.23.69.32    | 172.23.69.63      | 172.23.69.33 | 172.23.69.62  |
| VLAN30 – HR         | 10         | /28  | 255.255.255.240 | 172.23.69.64    | 172.23.69.79      | 172.23.69.65 | 172.23.69.78  |
| MLS3 ↔ BR1          | 2          | /30  | 255.255.255.252 | 172.23.69.80    | 172.23.69.83      | 172.23.69.81 | 172.23.69.82  |
| MLS3 ↔ MLS4         | 2          | /30  | 255.255.255.252 | 172.23.69.84    | 172.23.69.87      | 172.23.69.85 | 172.23.69.86  |
| MLS3 ↔ MLS5         | 2          | /30  | 255.255.255.252 | 172.23.69.88    | 172.23.69.91      | 172.23.69.89 | 172.23.69.90  |

The `172.23.68.0/23` block was divided with VLSM, starting with the largest network: USERS uses a /24, STAFF and ACCOUNTING a /27 each, HR a /28 and each point-to-point link a /30. The networks do not overlap, and `172.23.69.92–172.23.69.255` is left free for the next sprints.

### 2.2 Interface Addressing Table

The tables list only the interfaces in use. On every switch, all other ports are unused: they are assigned to VLAN 99 (BLACKHOLE) and administratively shut down. On BR1, Gig0/1 is not used and is also shut down.

#### 2.2.1 BR1 (Warsaw)

| Interface | Assigned IP                | Subnet Mask | Purpose                                         |
| --------- | -------------------------- | ----------- | ----------------------------------------------- |
| Gig0/0    | 172.23.69.82               | /30         | Link to MLS3 (Gig0/1)                           |
| Gig0/0/0  | DHCP-assigned (192.0.2.97) | /28         | Internet (SP) connection — gateway 192.0.2.110 |

BR1 connects Warsaw to the Internet. Gig0/0 goes to MLS3, Gig0/0/0 gets its address, gateway and DNS server from the service provider through DHCP, and Gig0/1 is not used.

**Verification:** `show ip interface brief` shows Gig0/0 with 172.23.69.82, Gig0/0/0 with 192.0.2.97 (method DHCP).

#### 2.2.2 MLS3

| Interface | Assigned IP  | Subnet Mask | Purpose                                                                        |
| --------- | ------------ | ----------- | ------------------------------------------------------------------------------ |
| Gig0/1    | 172.23.69.81 | /30         | Routed port — link to BR1 (Warsaw) Gig0/0                                     |
| VLAN100   | 172.23.69.85 | /30         | Transit SVI — Layer 3 link to MLS4                                            |
| VLAN101   | 172.23.69.89 | /30         | Transit SVI — Layer 3 link to MLS5                                            |
| Po1       | —           | —          | LACP EtherChannel to MLS4 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/1     | —           | —          | Po1 member — to MLS4 Fa0/1                                                    |
| Fa0/2     | —           | —          | Po1 member — to MLS4 Fa0/2                                                    |
| Po2       | —           | —          | LACP EtherChannel to MLS5 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/3     | —           | —          | Po2 member — to MLS5 Fa0/3                                                    |
| Fa0/4     | —           | —          | Po2 member — to MLS5 Fa0/4                                                    |

MLS3 links the two distribution switches to BR1. Its link to BR1 is a routed port, and its links to MLS4 and MLS5 are EtherChannels with a Layer 3 address on VLAN 100 and VLAN 101.

**Verification:** `show ip interface brief` shows Gig0/1, Vlan100 and Vlan101 up with their addresses and the unused ports administratively down.

#### 2.2.3 MLS4

| Interface | Assigned IP  | Subnet Mask | Purpose                                                                        |
| --------- | ------------ | ----------- | ------------------------------------------------------------------------------ |
| VLAN10    | 172.23.69.2  | /27         | STAFF SVI — HSRP group 10, **active** (virtual IP 172.23.69.1)          |
| VLAN20    | 172.23.69.34 | /27         | ACCOUNTING SVI — HSRP group 20, **active** (virtual IP 172.23.69.33)    |
| VLAN30    | 172.23.69.66 | /28         | HR SVI — HSRP group 30, standby (virtual IP 172.23.69.65)                     |
| VLAN40    | 172.23.68.2  | /24         | USERS SVI — HSRP group 40, standby (virtual IP 172.23.68.1)                   |
| VLAN100   | 172.23.69.86 | /30         | Transit SVI — Layer 3 link to MLS3                                            |
| Po1       | —           | —          | LACP EtherChannel to MLS3 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/1     | —           | —          | Po1 member — to MLS3 Fa0/1                                                    |
| Fa0/2     | —           | —          | Po1 member — to MLS3 Fa0/2                                                    |
| Po3       | —           | —          | LACP EtherChannel to MLS5 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/3     | —           | —          | Po3 member — to MLS5 Fa0/1                                                    |
| Fa0/4     | —           | —          | Po3 member — to MLS5 Fa0/2                                                    |
| Fa0/5     | —           | —          | Trunk to SW3 Fa0/1                                                             |
| Fa0/6     | —           | —          | Trunk to SW4 Fa0/1                                                             |
| Fa0/7     | —           | —          | Trunk to SW5 Fa0/1                                                             |
| Fa0/8     | —           | —          | Trunk to SW6 Fa0/1                                                             |

MLS4 is one of the two gateways of the user networks. It has an interface in each user VLAN with the second address of the network; the first address is the gateway shared with MLS5 through HSRP. It connects to MLS3, to MLS5 and to the four access switches.

**Verification:** `show ip interface brief` shows the five SVIs up with the addresses in the table and the unused ports administratively down.

#### 2.2.4 MLS5

| Interface | Assigned IP  | Subnet Mask | Purpose                                                                        |
| --------- | ------------ | ----------- | ------------------------------------------------------------------------------ |
| VLAN10    | 172.23.69.3  | /27         | STAFF SVI — HSRP group 10, standby (virtual IP 172.23.69.1)                   |
| VLAN20    | 172.23.69.35 | /27         | ACCOUNTING SVI — HSRP group 20, standby (virtual IP 172.23.69.33)             |
| VLAN30    | 172.23.69.67 | /28         | HR SVI — HSRP group 30, **active** (virtual IP 172.23.69.65)            |
| VLAN40    | 172.23.68.3  | /24         | USERS SVI — HSRP group 40, **active** (virtual IP 172.23.68.1)          |
| VLAN101   | 172.23.69.90 | /30         | Transit SVI — Layer 3 link to MLS3                                            |
| Po3       | —           | —          | LACP EtherChannel to MLS4 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/1     | —           | —          | Po3 member — to MLS4 Fa0/3                                                    |
| Fa0/2     | —           | —          | Po3 member — to MLS4 Fa0/4                                                    |
| Po2       | —           | —          | LACP EtherChannel to MLS3 — 802.1Q trunk, native VLAN 50, all VLANs except 99 |
| Fa0/3     | —           | —          | Po2 member — to MLS3 Fa0/3                                                    |
| Fa0/4     | —           | —          | Po2 member — to MLS3 Fa0/4                                                    |
| Fa0/5     | —           | —          | Trunk to SW3 Fa0/2                                                             |
| Fa0/6     | —           | —          | Trunk to SW4 Fa0/2                                                             |
| Fa0/7     | —           | —          | Trunk to SW5 Fa0/2                                                             |
| Fa0/8     | —           | —          | Trunk to SW6 Fa0/2                                                             |

MLS5 is the second gateway of the user networks and mirrors MLS4, using the third address of each user network.

**Verification:** `show ip interface brief` shows the five SVIs up with the addresses in the table and the unused ports administratively down.

#### 2.2.5 SW3

| Interface      | Assigned IP | Subnet Mask | Purpose                                                       |
| -------------- | ----------- | ----------- | ------------------------------------------------------------- |
| Fa0/1          | —          | —          | Trunk to MLS4 Fa0/5 — native VLAN 50, all VLANs except 99    |
| Fa0/2          | —          | —          | Trunk to MLS5 Fa0/5 — native VLAN 50, all VLANs except 99    |
| Fa0/3–Fa0/5   | —          | —          | Access — VLAN 10 (STAFF), PortFast; Warsaw-STAFF PC on Fa0/3 |
| Fa0/6–Fa0/10  | —          | —          | Access — VLAN 20 (ACCOUNTING), PortFast                      |
| Fa0/11–Fa0/15 | —          | —          | Access — VLAN 30 (HR), PortFast                              |
| Fa0/16–Fa0/20 | —          | —          | Access — VLAN 40 (USERS), PortFast                           |

SW3 is an access switch with one uplink to MLS4 and one to MLS5, so it stays connected if one of them fails. The access ports are assigned to the four user VLANs. SW4, SW5 and SW6 have the same configuration.

**Verification:** `show vlan brief` shows each group of access ports in its VLAN and the unused ports in VLAN 99.

![SW3 – show vlan brief](prints/Warsaw/SW3_show_vlan_brief.png)

#### 2.2.6 SW4

| Interface      | Assigned IP | Subnet Mask | Purpose                                                                 |
| -------------- | ----------- | ----------- | ----------------------------------------------------------------------- |
| Fa0/1          | —          | —          | Trunk to MLS4 Fa0/6 — native VLAN 50, all VLANs except 99              |
| Fa0/2          | —          | —          | Trunk to MLS5 Fa0/6 — native VLAN 50, all VLANs except 99              |
| Fa0/3–Fa0/5   | —          | —          | Access — VLAN 10 (STAFF), PortFast                                     |
| Fa0/6–Fa0/10  | —          | —          | Access — VLAN 20 (ACCOUNTING), PortFast; Warsaw-ACCOUNTING PC on Fa0/6 |
| Fa0/11–Fa0/15 | —          | —          | Access — VLAN 30 (HR), PortFast                                        |
| Fa0/16–Fa0/20 | —          | —          | Access — VLAN 40 (USERS), PortFast                                     |

Same configuration as SW3. The Warsaw-ACCOUNTING PC is on Fa0/6.

**Verification:** `show vlan brief` shows the same port assignment as SW3.

#### 2.2.7 SW5

| Interface      | Assigned IP | Subnet Mask | Purpose                                                    |
| -------------- | ----------- | ----------- | ---------------------------------------------------------- |
| Fa0/1          | —          | —          | Trunk to MLS4 Fa0/7 — native VLAN 50, all VLANs except 99 |
| Fa0/2          | —          | —          | Trunk to MLS5 Fa0/7 — native VLAN 50, all VLANs except 99 |
| Fa0/3–Fa0/5   | —          | —          | Access — VLAN 10 (STAFF), PortFast                        |
| Fa0/6–Fa0/10  | —          | —          | Access — VLAN 20 (ACCOUNTING), PortFast                   |
| Fa0/11–Fa0/15 | —          | —          | Access — VLAN 30 (HR), PortFast; Warsaw-HR PC on Fa0/11   |
| Fa0/16–Fa0/20 | —          | —          | Access — VLAN 40 (USERS), PortFast                        |

Same configuration as SW3. The Warsaw-HR PC is on Fa0/11.

**Verification:** `show vlan brief` shows the same port assignment as SW3.

#### 2.2.8 SW6

| Interface      | Assigned IP | Subnet Mask | Purpose                                                        |
| -------------- | ----------- | ----------- | -------------------------------------------------------------- |
| Fa0/1          | —          | —          | Trunk to MLS4 Fa0/8 — native VLAN 50, all VLANs except 99     |
| Fa0/2          | —          | —          | Trunk to MLS5 Fa0/8 — native VLAN 50, all VLANs except 99     |
| Fa0/3–Fa0/5   | —          | —          | Access — VLAN 10 (STAFF), PortFast                            |
| Fa0/6–Fa0/10  | —          | —          | Access — VLAN 20 (ACCOUNTING), PortFast                       |
| Fa0/11–Fa0/15 | —          | —          | Access — VLAN 30 (HR), PortFast                               |
| Fa0/16–Fa0/20 | —          | —          | Access — VLAN 40 (USERS), PortFast; Warsaw-USERS PC on Fa0/16 |

Same configuration as SW3. The Warsaw-USERS PC is on Fa0/16.

**Verification:** `show vlan brief` shows the same port assignment as SW3.

#### 2.2.9 Warsaw-STAFF

| Interface | Assigned IP                 | Subnet Mask | Purpose                                   |
| --------- | --------------------------- | ----------- | ----------------------------------------- |
| Fa0       | DHCP-assigned (172.23.69.4) | /27         | VLAN10 host — gateway 172.23.69.1 (HSRP) |

The PC gets its configuration by DHCP from MLS4.

**Verification:** `ipconfig /all` (shown in 2.3.6).

#### 2.2.10 Warsaw-ACCOUNTING

| Interface | Assigned IP                  | Subnet Mask | Purpose                                    |
| --------- | ---------------------------- | ----------- | ------------------------------------------ |
| Fa0       | DHCP-assigned (172.23.69.36) | /27         | VLAN20 host — gateway 172.23.69.33 (HSRP) |

The PC gets its configuration by DHCP from MLS4.

**Verification:** `ipconfig /all`.

#### 2.2.11 Warsaw-HR

| Interface | Assigned IP                  | Subnet Mask | Purpose                                    |
| --------- | ---------------------------- | ----------- | ------------------------------------------ |
| Fa0       | DHCP-assigned (172.23.69.68) | /28         | VLAN30 host — gateway 172.23.69.65 (HSRP) |

The PC gets its configuration by DHCP from MLS5.

**Verification:** `ipconfig /all`.

#### 2.2.12 Warsaw-USERS

| Interface | Assigned IP                 | Subnet Mask | Purpose                                   |
| --------- | --------------------------- | ----------- | ----------------------------------------- |
| Fa0       | DHCP-assigned (172.23.68.4) | /24         | VLAN40 host — gateway 172.23.68.1 (HSRP) |

The PC gets its configuration by DHCP from MLS5.

**Verification:** `ipconfig /all`.

### 2.3 Configuration Decisions

#### 2.3.1 Addressing

- **VLSM, largest network first:** All Warsaw networks fit inside `172.23.68.0/23`. The range `172.23.69.92–172.23.69.255` is left free for later sprints.
- **Network sizes:** USERS (200) uses a /24, STAFF (30) and ACCOUNTING (20) a /27, HR (10) a /28 and the point-to-point links a /30.

#### 2.3.2 VLANs and VTP

- **VTP domain** `RECOMP2627M1B03`, password `7262pmocer`. MLS3, MLS4 and MLS5 are VTP servers; SW3–SW6 are VTP clients.
- **VLANs are created only on MLS4** and reach the other switches through VTP.

| VLAN | Name              | Use                                    |
| ---- | ----------------- | -------------------------------------- |
| 10   | STAFF             | User network                           |
| 20   | ACCOUNTING        | User network                           |
| 30   | HR                | User network                           |
| 40   | USERS             | User network                           |
| 50   | NATIVE            | Native VLAN on every trunk             |
| 99   | BLACKHOLE         | Unused ports; not allowed on any trunk |
| 100  | TRANSIT-MLS3-MLS4 | Layer 3 transit between MLS3 and MLS4  |
| 101  | TRANSIT-MLS3-MLS5 | Layer 3 transit between MLS3 and MLS5  |

VTP keeps the same list of VLANs on every Warsaw switch. The VLANs were created once on MLS4 and the other switches received them automatically: MLS3 and MLS5 as servers and the four access switches as clients.

**Verification:** `show vtp status` shows the same domain, revision and MD5 digest on every switch, with Server mode on MLS3–MLS5 and Client mode on SW3–SW6.

**MLS4 – `show vtp status` (server)**

![MLS4 – show vtp status](prints/Warsaw/MLS4_show_vtp_status.png)

**SW3 – `show vtp status` (client with the same revision and MD5 digest)**

![SW3 – show vtp status](prints/Warsaw/SW3_show_vtp_status.png)

#### 2.3.3 Interconnection between MLS3, MLS4 and MLS5

- **Layer 2:** MLS3, MLS4 and MLS5 are connected to each other by three LACP EtherChannels, configured as 802.1Q trunks (native VLAN 50, all VLANs except 99).
- **Layer 3:** MLS3 reaches MLS4 through VLAN 100 and MLS5 through VLAN 101. Each of these VLANs has a /30 network and an SVI on both ends, so MLS3 routes to MLS4 and MLS5 over the EtherChannels.

| Port-channel | Between      | Ports                          | Protocol |
| ------------ | ------------ | ------------------------------ | -------- |
| Po1          | MLS3 ↔ MLS4 | MLS3 Fa0/1–2 ↔ MLS4 Fa0/1–2 | LACP     |
| Po2          | MLS3 ↔ MLS5 | MLS3 Fa0/3–4 ↔ MLS5 Fa0/3–4 | LACP     |
| Po3          | MLS4 ↔ MLS5 | MLS4 Fa0/3–4 ↔ MLS5 Fa0/1–2 | LACP     |

Each EtherChannel joins two cables into one logical link. This doubles the bandwidth, keeps the link working if one cable fails, and lets Spanning Tree treat the two cables as a single link.

**Verification:** `show etherchannel summary` shows every Port-channel in use (SU) with LACP and both ports bundled (P). `show interfaces trunk` shows native VLAN 50 and VLAN 99 not allowed. A ping from MLS3 to 172.23.69.86 and 172.23.69.90 confirms the Layer 3 links.

**MLS4 – `show etherchannel summary` (Po1 and Po3)**

![MLS4 – show etherchannel summary](prints/Warsaw/MLS4_show_etherchannel_summary.png)

**MLS5 – `show etherchannel summary` (Po2 and Po3)**

![MLS5 – show etherchannel summary](prints/Warsaw/MLS5_show_etherchannel_summary.png)

#### 2.3.4 Rapid-PVST+ and HSRP

- **Rapid-PVST+** runs on every switch. The HSRP active switch of each VLAN is the STP root of that VLAN, as required.
- **HSRP:** Each user VLAN has its own HSRP group, numbered like the VLAN; the active switch has priority 110 and both switches use `preempt`, so the active role returns after a failure.
- **MLS3 keeps the default STP priority** and is never root for VLANs 10–40, 100 and 101.
- **Access ports use PortFast**, so hosts get their DHCP lease without waiting for STP.

| VLAN | STP root (primary) | STP secondary root | HSRP active | Virtual IP   |
| ---- | ------------------ | ------------------ | ----------- | ------------ |
| 10   | MLS4               | MLS5               | MLS4        | 172.23.69.1  |
| 20   | MLS4               | MLS5               | MLS4        | 172.23.69.33 |
| 30   | MLS5               | MLS4               | MLS5        | 172.23.69.65 |
| 40   | MLS5               | MLS4               | MLS5        | 172.23.68.1  |
| 100  | MLS4               | —                 | —          | —           |
| 101  | MLS5               | —                 | —          | —           |

**Rapid-PVST+.** Each access switch is connected to both MLS4 and MLS5, which creates loops. Rapid-PVST+ blocks the extra path in each VLAN and opens it again in one or two seconds if the active path fails. Because MLS4 is the root for VLANs 10 and 20 and MLS5 for VLANs 30 and 40, both uplinks of every access switch are used.

**Verification:** `show spanning-tree vlan 10` on MLS4 and `show spanning-tree vlan 30` on MLS5 show *"This bridge is the root"*.

**MLS4 – `show spanning-tree vlan 10` (root)**

![MLS4 – show spanning-tree vlan 10](prints/Warsaw/MLS4_show_spanning-tree_vlan10.png)

**MLS5 – `show spanning-tree vlan 30` (root)**

![MLS5 – show spanning-tree vlan 30](prints/Warsaw/MLS5_show_spanning-tree_vlan30.png)

**HSRP.** MLS4 and MLS5 share a virtual IP address in each user VLAN, and the PCs use it as their default gateway. One switch is active and answers for that address; the other is standby and takes over if the active one fails, so the PCs keep working with the same gateway. When the failed switch comes back, it becomes active again.

**Verification:** `show standby brief` on MLS4 shows VLANs 10 and 20 as Active and VLANs 30 and 40 as Standby. The failover test in 2.4 shows MLS5 taking over when MLS4 is isolated.

**MLS4 – `show standby brief` (MLS4 active for VLANs 10 and 20, MLS5 active for VLANs 30 and 40)**

![MLS4 – show standby brief](prints/Warsaw/MLS4_show_standby_brief.png)

#### 2.3.5 Routing

`ip routing` is enabled on MLS3, MLS4 and MLS5. All routes are static.

| Device | Destination    | Next hop                      | Notes                               |
| ------ | -------------- | ----------------------------- | ----------------------------------- |
| MLS4   | 0.0.0.0/0      | 172.23.69.85 (MLS3)           | Default route                       |
| MLS5   | 0.0.0.0/0      | 172.23.69.89 (MLS3)           | Default route                       |
| MLS3   | 172.23.68.0/23 | 172.23.69.86 and 172.23.69.90 | Equal-cost routes via MLS4 and MLS5 |
| MLS3   | 0.0.0.0/0      | 172.23.69.82 (BR1)            | Default route                       |
| BR1    | 172.23.68.0/23 | 172.23.69.81 (MLS3)           | Return route to the Warsaw networks |
| BR1    | 0.0.0.0/0      | 192.0.2.110 (SP)              | Learned from the SP through DHCP    |

MLS4 and MLS5 route between the user VLANs and send all other traffic to MLS3. MLS3 reaches the user networks through both MLS4 and MLS5 and sends the rest to BR1. BR1 sends the traffic for Warsaw back to MLS3 and uses the default route received from the service provider for everything else.

**Verification:** `show ip route` on MLS3 shows the two routes to `172.23.68.0/23` and the default route to BR1; on BR1 it shows the route to Warsaw and the default route learned from the service provider.

**MLS3 – `show ip route`**

![MLS3 – show ip route](prints/Warsaw/MLS3_show_ip_route.png)

**BR1 – `show ip route`**

![BR1 – show ip route](prints/Warsaw/BR1_show_ip_route.png)

#### 2.3.6 DHCP

Identical pools on MLS4 and MLS5. Every pool uses DNS server `8.8.8.8` and domain `RECOMP2627M1B03.recomp.com`.

| Pool       | Network         | Default gateway (HSRP) | Excluded                   |
| ---------- | --------------- | ---------------------- | -------------------------- |
| STAFF      | 172.23.69.0/27  | 172.23.69.1            | 172.23.69.1–172.23.69.3   |
| ACCOUNTING | 172.23.69.32/27 | 172.23.69.33           | 172.23.69.33–172.23.69.35 |
| HR         | 172.23.69.64/28 | 172.23.69.65           | 172.23.69.65–172.23.69.67 |
| USERS      | 172.23.68.0/24  | 172.23.68.1            | 172.23.68.1–172.23.68.3   |

Each PC receives an address from its VLAN, the HSRP virtual IP as default gateway, DNS server 8.8.8.8 and the domain of the group. The gateway and switch addresses are excluded from the pools. Because MLS4 and MLS5 have the same pools, the PCs still get an address if one of them fails.

**Verification:** `ipconfig /all` on each PC shows the address, the gateway, the DNS server and the domain. `show ip dhcp binding` on MLS4 and MLS5 lists the addresses given to the PCs.

**Warsaw-STAFF – `ipconfig /all`**

![Warsaw-STAFF – ipconfig /all](prints/Warsaw/PC-STAFF_ipconfig_all.png)

### 2.4 Connectivity Tests

| Source                        | Destination                            | Test Type                           | Result                                                |
| ----------------------------- | -------------------------------------- | ----------------------------------- | ----------------------------------------------------- |
| BR1 Router                    | SP gateway (192.0.2.110)               | `ping`                            | Success                                               |
| BR1 Router                    | www.google.com                         | `ping`                            | Success                                               |
| Warsaw PC (STAFF)             | Warsaw PC (ACCOUNTING) — 172.23.69.36 | `ping`                            | Success                                               |
| Warsaw PC (STAFF)             | Warsaw PC (HR) — 172.23.69.68         | `ping`                            | Success                                               |
| Warsaw PC (STAFF)             | Warsaw PC (USERS) — 172.23.68.4       | `ping`                            | Success                                               |
| Warsaw PC (STAFF)             | BR1 Gig0/0 (172.23.69.82)              | `ping`                            | Success                                               |
| Warsaw PC (STAFF)             | Warsaw PC (HR) — 172.23.69.68         | `tracert`                         | Success — first hop 172.23.69.2 (MLS4, HSRP active)  |
| Warsaw PC (ACCOUNTING)        | Warsaw PC (STAFF) — 172.23.69.4       | `ping`                            | Success                                               |
| Warsaw PC (ACCOUNTING)        | Warsaw PC (HR) — 172.23.69.68         | `ping`                            | Success                                               |
| Warsaw PC (ACCOUNTING)        | Warsaw PC (USERS) — 172.23.68.4       | `ping`                            | Success                                               |
| Warsaw PC (ACCOUNTING)        | BR1 Gig0/0 (172.23.69.82)              | `ping`                            | Success                                               |
| Warsaw PC (ACCOUNTING)        | Warsaw PC (USERS) — 172.23.68.4       | `tracert`                         | Success — first hop 172.23.69.34 (MLS4, HSRP active) |
| Warsaw PC (HR)                | Warsaw PC (STAFF) — 172.23.69.4       | `ping`                            | Success                                               |
| Warsaw PC (HR)                | Warsaw PC (ACCOUNTING) — 172.23.69.36 | `ping`                            | Success                                               |
| Warsaw PC (HR)                | Warsaw PC (USERS) — 172.23.68.4       | `ping`                            | Success                                               |
| Warsaw PC (HR)                | BR1 Gig0/0 (172.23.69.82)              | `ping`                            | Success                                               |
| Warsaw PC (HR)                | Warsaw PC (STAFF) — 172.23.69.4       | `tracert`                         | Success — first hop 172.23.69.67 (MLS5, HSRP active) |
| Warsaw PC (USERS)             | Warsaw PC (STAFF) — 172.23.69.4       | `ping`                            | Success                                               |
| Warsaw PC (USERS)             | Warsaw PC (ACCOUNTING) — 172.23.69.36 | `ping`                            | Success                                               |
| Warsaw PC (USERS)             | Warsaw PC (HR) — 172.23.69.68         | `ping`                            | Success                                               |
| Warsaw PC (USERS)             | BR1 Gig0/0 (172.23.69.82)              | `ping`                            | Success                                               |
| Warsaw PC (USERS)             | Warsaw PC (ACCOUNTING) — 172.23.69.36 | `tracert`                         | Success — first hop 172.23.68.3 (MLS5, HSRP active)  |
| HSRP failover (MLS4 isolated) | Warsaw PC (STAFF) → Warsaw PC (HR)    | `ping -t`, `show standby brief` | MLS5 became active for VLANs 10 and 20                |

BR1 reaches the service provider and www.google.com, and the PCs of the four Warsaw networks reach each other and BR1. Between PCs the replies arrive with TTL=127, which means one router in the path (MLS4 or MLS5). The first hop of each `tracert` is the active HSRP switch of that VLAN: MLS4 for STAFF and ACCOUNTING, MLS5 for HR and USERS. When MLS4 is isolated, MLS5 becomes active for VLANs 10 and 20. The prints below show the tests from Warsaw-STAFF and the traceroute from Warsaw-HR; the tests from the other PCs gave the same results.

**BR1 → SP gateway and www.google.com**

![](prints/Warsaw/Ligação_SP_Google_Warsaw.png)

**Warsaw PC (STAFF): DHCP-assigned address and ping to ACCOUNTING**

![](prints/Warsaw/Ping_PCSTAFF_Warsaw.png)

**Warsaw PC (STAFF): ping to HR and USERS**

![](prints/Warsaw/Ping_PCSTAFF_Warsaw_2.png)

**Warsaw PC (STAFF): ping to BR1 and traceroute to HR (first hop MLS4)**

![](prints/Warsaw/Traceroute_Warsaw.png)

**Warsaw PC (HR): traceroute to STAFF (first hop MLS5)**

![Warsaw-HR – tracert to STAFF](prints/Warsaw/PC-HR_tracert_STAFF.png)

**HSRP failover: MLS4 isolated, MLS5 takes over VLANs 10 and 20**

![](prints/Failover_Warsaw.png)

### 2.5 Configuration Verification

| Command                       | Device(s)        | Confirms                                                       |
| ----------------------------- | ---------------- | -------------------------------------------------------------- |
| `show vtp status`           | MLS4, SW3        | VTP domain, server/client mode, synchronized revision          |
| `show vlan brief`           | MLS4, SW3        | VLAN names and access-port membership; unused ports in VLAN 99 |
| `show etherchannel summary` | MLS3, MLS4, MLS5 | Po1, Po2, Po3 in use (SU), LACP, all members bundled (P)       |
| `show interfaces trunk`     | MLS4, SW3        | 802.1Q, native VLAN 50, VLAN 99 not allowed                    |
| `show spanning-tree vlan X` | MLS4, MLS5       | Root bridges per VLAN as in 2.3.4                              |
| `show standby brief`        | MLS4, MLS5       | HSRP active/standby roles as in 2.3.4                          |
| `show ip route`             | MLS3, BR1        | Static and default routes as in 2.3.5                          |
| `show ip dhcp binding`      | MLS4, MLS5       | Leases handed to the Warsaw PCs                                |

<!-- ![](prints/warsaw_verification.jpg) -->

---

## 3. Munich (BR2)

![](sites/munich.jpg)

### <u></u> 192.186.166.0/23

### 3.1 Network Addressing Table

| Network              | Hosts req. | CIDR | Mask            | Network Address | Broadcast Address | First Host     | Last Host       |
| -------------------- | ---------- | ---- | --------------- | --------------- | ----------------- | -------------- | --------------- |
| VLAN40 – USERS      | 200        | /24  | 255.255.255.0   | 192.186.166.0   | 192.186.166.255   | 192.186.166.1  | 192.186.166.254 |
| VLAN20 – ACCOUNTING | 20         | /27  | 255.255.255.224 | 192.186.167.0   | 192.186.167.31    | 192.186.167.1  | 192.186.167.30  |
| VLAN10 – STAFF      | 10         | /28  | 255.255.255.240 | 192.186.167.32  | 192.186.167.47    | 192.186.167.33 | 192.186.167.46  |
| VLAN30 – HR         | 10         | /28  | 255.255.255.240 | 192.186.167.48  | 192.186.167.63    | 192.186.167.49 | 192.186.167.62  |
| BR2 ↔ Campus1       | 2          | /30  | 255.255.255.252 | 192.186.167.64  | 192.186.167.67    | 192.186.167.65 | 192.186.167.66  |

##### 3.2 Configuration Decisions

###### 3.2.1 BR2 router: Internet (SP) connection (Gig0/0/0)

* **Static addressing:**  The Munich edge router BR2 connects to the Service Provider via interface Gig0/0/0 using static IP address 193.136.60.147/29 with gateway 193.136.60.150.
* **Default route:**  Configured static default route `ip route 0.0.0.0 0.0.0.0 193.136.60.150` pointing to the SP gateway.

###### 3.2.2 BR2 and Campus1 routers: internal interfaces and routing

* **Point-to-point link:**  Configured a /30 subnet (192.186.167.64/30) on the link between BR2 (Gig0/0: 192.186.167.65) and Campus1 (Gig0/0: 192.186.167.66).
* **Campus1 default route:**  Configured `ip route 0.0.0.0 0.0.0.0 192.186.167.65` on Campus1 so all outbound traffic is forwarded to BR2.
* **BR2 return route:**  Configured static route `ip route 192.186.166.0 255.255.255.0 192.186.167.66` on BR2 to reach the USERS subnet behind Campus1.
* **Router-on-a-Stick (BR2):**  Configured dot1Q subinterfaces Gig0/1.10, Gig0/1.20, and Gig0/1.30 on BR2 to serve as default gateways for STAFF, ACCOUNTING, and HR using the last available host address in each subnet.
* **Campus1 LAN interface:**  Configured Gig0/1 on Campus1 with IP 192.186.166.254/24 (last available host address) as the default gateway for USERS (VLAN 40).
* **DHCP Pools:**  Configured DHCP pools on BR2 for STAFF, ACCOUNTING, and HR, and on Campus1 for USERS. Excluded gateway IPs and configured DNS server 8.8.8.8 and domain name RECOMP2627M1B03.recomp.com across all pools.

###### 3.2.3 Switches SW7 and SW8: VLANs and ports

* **SW7 (Trunk Switch):**  Configured Gig0/1 as an 802.1Q trunk to BR2 with Native VLAN 50 and allowed VLANs 10, 20, 30, and 50. Configured Fa0/1-Fa0/5 for VLAN 10 (STAFF), Fa0/6-Fa0/10 for VLAN 20 (ACCOUNTING), and Fa0/11-Fa0/15 for VLAN 30 (HR).
* **SW8 (Access Switch):**  Configured Gig0/1 as an access port in VLAN 40 (USERS) connected to Campus1 Gig0/1. Configured Fa0/1-Fa0/24 as access ports for VLAN 40.
* **VLAN 99 (BLACKHOLE) created locally:**  Created VLAN 99 on both switches for port security. All unused ports (Fa0/16–Fa0/24 & Gig0/2 on SW7, Gig0/2 on SW8) are assigned to VLAN 99 and administratively shut down. No trunk allows VLAN 99.

### 3.3 Interface Addressing Table

#### 3.3.1 BR2 (Munich)

| Interface | Assigned IP    | Subnet Mask | Purpose                                     |
| --------- | -------------- | ----------- | ------------------------------------------- |
| Gig0/0    | 192.186.167.65 | /30         | Link to Campus1 (Munich)                   |
| Gig0/1.10 | 192.186.167.46 | /28         | STAFF gateway (last available address)      |
| Gig0/1.20 | 192.186.167.30 | /27         | ACCOUNTING gateway (last available address) |
| Gig0/1.30 | 192.186.167.62 | /28         | HR gateway (last available address)         |
| Gig0/0/0  | 193.136.60.147 | /29         | Internet (SP) connection - static           |

#### 3.2.2 Campus1 (Munich)

| Interface | Assigned IP    | Subnet Mask | Purpose                                |
| --------- | -------------- | ----------- | -------------------------------------- |
| Gig0/0    | 192.186.167.66 | /30         | Link to BR2 (Munich)                  |
| Gig0/1    | 192.186.166.1  | /24         | USERS gateway (last available address) |

#### 3.3.3 SW7

| Interface              | Purpose                                                             |
| ---------------------- | ------------------------------------------------------------------- |
| Gig0/1                 | Trunk to BR2 Gig0/1 - native VLAN 50, allowed VLANs 10, 20, 30, 50 |
| Fa0/1–Fa0/5           | Access - VLAN 10 (STAFF)                                            |
| Fa0/6–Fa0/10          | Access - VLAN 20 (ACCOUNTING)                                       |
| Fa0/11–Fa0/15         | Access - VLAN 30 (HR)                                               |
| Fa0/16–Fa0/24, Gig0/2 | Unused - VLAN 99, administratively shut down                        |

#### 3.3.4 SW8

| Interface     | Purpose                                          |
| ------------- | ------------------------------------------------ |
| Gig0/1        | Access link to Campus1 Gig0/1 - VLAN 40 (USERS) |
| Fa0/1–Fa0/24 | Access - VLAN 40 (USERS)                         |
| Gig0/2        | Unused - VLAN 99, administratively shut down     |

#### 3.3.5 Munich-USERS

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /24         | VLAN40 host |

#### 3.3.6 Munich-ACCOUNTING

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /27         | VLAN20 host |

#### 3.3.7 Munich-STAFF

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /28         | VLAN10 host |

#### 3.3.8 Munich-HR

| Interface | Assigned IP   | Subnet Mask | Purpose     |
| --------- | ------------- | ----------- | ----------- |
| Fa0       | DHCP-assigned | /28         | VLAN30 host |

### 3.3 Connectivity Tests

![1792267252422](image/report/1792267252422.png)

![1792267257302](image/report/1792267257302.png)

![1792267264117](image/report/1792267264117.png)![1792267275350](image/report/1792267275350.png)![1792267278871](image/report/1792267278871.png)![1792267282271](image/report/1792267282271.png)

---

## 4. The Vault

![](sites/vault.jpg)

### <u></u> 10.31.111.0/24

### 4.1 Configuration Decisions

#### 4.1.1 Vault router: Internet (SP) connection (Gig0/0/0)

- **Static addressing:** The Vault is the one site whose SP link is configured manually, not by DHCP. I used the values given in the problem statement: `203.0.113.2/30`, with `203.0.113.1` as the gateway.
- **Default route:** I configured `ip route 0.0.0.0 0.0.0.0 203.0.113.1`. The Vault has a single exit toward the Internet, so one default route is enough and no routing protocol is needed.

#### 4.1.2 Vault router: internal interface (Gig0/1)

- I left Gig0/1 **unaddressed and administratively shut down**. The brief says the Vault’s internal details “will be disclosed as required during the project,” so there is no network to serve yet.
- I did **not** assign any address from `10.31.111.0/24`. The block is reserved for later sprints.
- Keeping an unneeded interface shut down also stops it from being used as an unintended entry point.

#### 4.1.3 Vault switch: VLANs and ports

- **VLAN 99 (BLACKHOLE) created locally:** The Vault is a separate site and does not share a VTP domain with HQ, Warsaw or Munich, so VLAN 99 is not inherited and had to be created on this switch.
- **All unused ports are assigned to VLAN 99 and shut down:** This covers `Fa0/1–Fa0/24`, `Gig0/1` and `Gig0/2`. Since the Vault has no internal hosts yet, every port, including the router uplink, counts as unused.
- **VLAN 1 not used:** No port is left in the default VLAN 1, as instructed. `show vlan brief` shows no interfaces in VLAN 1.
- **No trunks configured:** The only link, Router Gig0/1 ↔ Switch Gig0/1, carries no tagged traffic, so no trunk exists.

### 4.2 Interface Addressing Table

#### 4.2.1 Vault (unknown location)

| Interface | Assigned IP      | Subnet Mask | Purpose                                           |
| --------- | ---------------- | ----------- | ------------------------------------------------- |
| Gig0/0/0  | 203.0.113.2      | /30         | Internet (SP) connection — static                |
| Gig0/1    | *(unassigned)* | —          | Link to Vault Switch (administratively shut down) |

#### 4.2.2 Vault Switch

| Interface | Purpose                                                                         |
| --------- | ------------------------------------------------------------------------------- |
| Gig0/1    | Link to Vault Router (unused - assigned to VLAN 99, administratively shut down) |

### 4.3 Connectivity Tests

| Source       | Destination              | Test Type | Result  |
| ------------ | ------------------------ | --------- | ------- |
| Vault Router | SP gateway (203.0.113.1) | `ping`  | Success |
| Vault Router | www.google.com           | `ping`  | Success |

![](prints/vault_connectivity_tests.jpg)
