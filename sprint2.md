### 3. OSPF

#### 3.1 OSPF Configuration

To configure OSPF in Oporto and Warsaw, we did the following:
- Started an OSPF process with ID *1* on every router and MLS in both locations. The process ID is only locally significant, but using the same value everywhere keeps the configuration consistent.
- Manually defined a *router-id* on each device, instead of letting OSPF choose one from the interface addresses. This makes each router easy to identify in the neighbour tables and the OSPF database, and keeps the ID stable if interfaces change:
    - Oporto: *RT Oporto* 1.1.1.1, *MLS1* 1.1.1.2 and *MLS2* 1.1.1.3;
    - Warsaw: *RT Warsaw* 2.2.2.1, *MLS3* 2.2.2.2, *MLS4* 2.2.2.3 and *MLS5* 2.2.2.4.
- Advertised the tunnel network between Oporto and Warsaw (*10.0.0.0/30*) in *area 0*, the backbone area, since it interconnects both sites.
- Advertised the point-to-point links between each router and its MLSs in *area 0* as well, extending the backbone into each site.
- Advertised Oporto's local networks (VLANs 10, 20, 30 and 40) in *area 1* and Warsaw's local networks in *area 2*.

With this design, the MLSs that hold both backbone links and local networks act as *Area Border Routers (ABRs)*: *MLS1* and *MLS2* in Oporto, and *MLS4* and *MLS5* in Warsaw. *MLS3* only has links in area 0, acting as a backbone router that connects *RT Warsaw* to *MLS4* and *MLS5*. Splitting each site into its own area keeps the link-state database smaller and means topology changes inside one site do not trigger SPF recalculations in the other.

The wildcard masks used in the *network* commands are the inverse of each subnet mask. For example, *0.0.0.3* matches a /30 link and *0.0.1.255* matches the /23 network of the USERS VLAN.

- RT Oporto

```
router ospf 1
 router-id 1.1.1.1
 network 10.42.59.192 0.0.0.3 area 0
 network 10.42.59.196 0.0.0.3 area 0
 network 10.0.0.0 0.0.0.3 area 0
```

- HQ_MLS1

```
router ospf 1
 router-id 1.1.1.2
 network 10.42.59.192 0.0.0.3 area 0
 network 10.42.59.128 0.0.0.63 area 1
 network 10.42.58.0 0.0.0.255 area 1
 network 10.42.59.0 0.0.0.127 area 1
 network 10.42.56.0 0.0.1.255 area 1
```

- HQ_MLS2

```
router ospf 1
 router-id 1.1.1.3
 network 10.42.59.196 0.0.0.3 area 0
 network 10.42.59.128 0.0.0.63 area 1
 network 10.42.58.0 0.0.0.255 area 1
 network 10.42.59.0 0.0.0.127 area 1
 network 10.42.56.0 0.0.1.255 area 1
```

- RT Warsaw

```
router ospf 1
 router-id 2.2.2.1
 network 172.23.69.80 0.0.0.3 area 0
 network 10.0.0.0 0.0.0.3 area 0
```

- Warsaw_MLS3

```
router ospf 1
 router-id 2.2.2.2
 network 172.23.69.80 0.0.0.3 area 0
 network 172.23.69.84 0.0.0.3 area 0
 network 172.23.69.88 0.0.0.3 area 0
```

- Warsaw_MLS4

```
router ospf 1
 router-id 2.2.2.3
 network 172.23.69.84 0.0.0.3 area 0
 network 172.23.69.0 0.0.0.31 area 2
 network 172.23.69.32 0.0.0.31 area 2
 network 172.23.69.64 0.0.0.15 area 2
 network 172.23.68.0 0.0.0.255 area 2
```

- Warsaw_MLS5

```
router ospf 1
 router-id 2.2.2.4
 network 172.23.69.88 0.0.0.3 area 0
 network 172.23.69.0 0.0.0.31 area 2
 network 172.23.69.32 0.0.0.31 area 2
 network 172.23.69.64 0.0.0.15 area 2
 network 172.23.68.0 0.0.0.255 area 2
```

To confirm that OSPF was correctly configured, the following tests were carried out:

- On *RT Oporto*, we ran the command *show ip ospf neighbor* to check that adjacencies were formed with *MLS1*, *MLS2* and, through the tunnel, with *RT Warsaw*:

![](prints/Routing/OSPF-Neighbors-Oporto.png)

All neighbours are in the *FULL* state, identified by the router-ids configured above.

- On *RT Warsaw*, we ran the same command to confirm the adjacencies with *MLS3* and *RT Oporto*:

![](prints/Routing/OSPF-Neighbors-Warsaw.png)

- On *RT Oporto*, we ran *show ip route ospf* to verify that Warsaw's networks were being learned:

![](prints/Routing/OSPF-Routes-Oporto.png)

Warsaw's local networks appear as *O IA* (inter-area) routes, which is the expected result, since they belong to area 2 and are advertised into the backbone by *MLS4* and *MLS5*.

- On one of the ABRs, we ran *show ip ospf* to confirm that it is participating in both areas:

![](prints/Routing/OSPF-ABR.png)

The output identifies the device as an *area border router* with interfaces in area 0 and in its local area.

#### 3.2 Connectivity Tests

- Oporto to Warsaw

![](prints/Routing/Oporto-Warsaw.png)

As is presented, connectivity between the hosts in Oporto and Warsaw was achieved through the OSPF backbone.

### 4. EIGRP + Redistribution

#### 4.1 EIGRP Configuration

To configure EIGRP between Oporto, Munich and the Vault, we did the following:
- Started an EIGRP process with *autonomous system number 1* on *RT Oporto (HQ)*, *BR2 Munich*, *Campus1 Munich* and the *Vault*. Unlike the OSPF process ID, the AS number must match on all routers, otherwise they do not form neighbour relationships.
- On *RT Oporto*, advertised the tunnel networks towards Munich (*10.0.0.4/30*) and towards the Vault (*10.0.0.8/30*).
- On *BR2 Munich*, advertised the tunnel network towards Oporto, Munich's local networks and the link towards *Campus1 Munich* (*192.186.167.64/30*).
- On *Campus1 Munich*, advertised the link towards *BR2 Munich* and the campus local network (*192.186.166.0/24*).
- On the *Vault*, advertised the tunnel network towards Oporto.

As in OSPF, the *network* commands include a wildcard mask, so only the intended interfaces take part in EIGRP, instead of the whole classful network.

- RT Oporto (HQ)

```
router eigrp 1
 network 10.0.0.4 0.0.0.3
 network 10.0.0.8 0.0.0.3
```

- BR2 Munich

```
router eigrp 1
 network 10.0.0.4 0.0.0.3
 network 192.186.167.32 0.0.0.15
 network 192.186.167.0 0.0.0.31
 network 192.186.167.48 0.0.0.15
 network 192.186.167.64 0.0.0.3
```

- Campus1 Munich

```
router eigrp 1
 network 192.186.167.64 0.0.0.3
 network 192.186.166.0 0.0.0.255
```

- Vault

```
router eigrp 1
 network 10.0.0.8 0.0.0.3
```

To confirm that EIGRP was correctly configured, the following tests were carried out:

- On *RT Oporto*, we ran the command *show ip eigrp neighbors* to check that neighbour relationships were formed with *BR2 Munich* and the *Vault* through the tunnel interfaces:

![](prints/Routing/EIGRP-Neighbors-Oporto.png)

- On *BR2 Munich*, we ran the same command to confirm the adjacencies with *RT Oporto* and *Campus1 Munich*:

![](prints/Routing/EIGRP-Neighbors-Munich.png)

- On *RT Oporto*, we ran *show ip route eigrp* to verify that Munich's networks were being learned:

![](prints/Routing/EIGRP-Routes-Oporto.png)

Munich's networks appear in the routing table with the code *D*, meaning they were learned through EIGRP.

#### 4.2 Route Redistribution

At this point, *RT Oporto* is the only device running both OSPF and EIGRP, so it knows every network, but the routing domains are still isolated from each other: Warsaw does not know how to reach Munich or the Vault, and vice versa. To achieve full connectivity, we configured mutual redistribution on *RT Oporto*:

```
router ospf 1
 redistribute eigrp 1 subnets
!
router eigrp 1
 redistribute ospf 1 metric 10000 1 255 1 1500
!
```

In this section, we did the following:
- Inside the OSPF process, redistributed the routes learned through EIGRP. The *subnets* keyword makes OSPF include the subnetted networks, instead of only classful networks, which would leave most of Munich's routes out.
- Inside the EIGRP process, redistributed the routes learned through OSPF. Since OSPF uses cost and EIGRP uses a composite metric, the two cannot be converted directly, so a *seed metric* had to be defined. Without it, the redistributed routes would be considered unreachable. The values used are, in order:
    - *Bandwidth*: 10000 kbps;
    - *Delay*: 1;
    - *Reliability*: 255;
    - *Load*: 1;
    - *MTU*: 1500 bytes.

This way, *RT Oporto* acts as an *Autonomous System Boundary Router (ASBR)* in OSPF, advertising the EIGRP networks into the OSPF domain, while advertising the OSPF networks to the EIGRP neighbours.

To confirm that redistribution was working, the following tests were carried out:

- On *RT Warsaw*, we ran the command *show ip route ospf 1 | include E2* to verify that Munich's and the Vault's networks were being learned:

![](prints/Routing/Redistribution-Warsaw.png)

These networks appear as *O E2* routes, meaning they are external routes injected into OSPF by an ASBR, in this case *RT Oporto*.

- On *BR2 Munich*, we ran the command *show ip route eigrp* to verify that Oporto's and Warsaw's networks were being learned:

![](prints/Routing/Redistribution-Munich.png)

These networks appear as *D EX* routes, meaning they are external routes redistributed into EIGRP.

#### 4.3 Connectivity Tests

- Oporto HR to Munich USERS

![](prints/Routing/Oporto-Munich.png)

- Warsaw USERS to Munich ACCOUNTING

![](prints/Routing/Warsaw-Munich.png)

- Munich (BR2 STAFF) to Munich (Campus1 Users)

![](prints/Routing/BR2-Campus1.png)

- Warsaw HR to the Vault

![](prints/Routing/Warsaw-Vault.png)

As is presented, connectivity between hosts in all locations was achieved.
