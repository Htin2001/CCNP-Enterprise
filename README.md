# CCNP-Enterprise
CCNP Labs / Theories

This includes daily updates of CCNP Enterprise's Labs and Theories.

# Day 1 (Static Route with AD Tuning)
<img width="1127" height="381" alt="Day 1" src="https://github.com/user-attachments/assets/23a8ca39-b7a2-4472-9904-e7de18b269b4" />

**Download Lab File (Packet Tracer File)**
[Day1 - Path Determination Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/0808fb684c1ed7f3fd19f70b78272796320a52c4/Path%20Determination%20(Htin%20Aung%20Lin).pkt) 

According to this lab, we want to make a connection between PC_0 and PC_1 and we use static routes for all routers and setting AD number - 2 for the backup route which is also called AD Tuning. 

`ip route (destination network) (subnetmask) (next hop / interface name) 2-255`

**Where do we use the Administrative Distance?**
- We use AD number in choosing of best route including backup route.
- We use AD number in comparing of Routing Protocol (i.e. Three routers running both OSPF and EIGRP, we want to choose EIGRP route for these three routers, we compare AD numbers at that time. So AD number of EIGRP is 90 / 170 which AD number of OSPF is 110. Routers will choose EIGRP for routing)
 

# Day 2 (RIP Route - RIPv2 Authentication - Accesslist)

<img width="857" height="585" alt="RIPv2" src="https://github.com/user-attachments/assets/dea83b62-38a2-449a-b07a-15113493f9ed" />


**Download Lab File (Eve File)**
[Day 2 RIP - RIPv2 Authentication Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/f4fb155d218f9d45e06e026d102d81d34ddd4847/RIP%20_%20RIPv2%20Authenticatioin%20Lab.zip)

On day 2, we discussed about the details of RIP routing protocol. 

**RIP V1**

- RIP is Open Standard Protocol.
- RIP v1 is a classfull routing protocol which doesn't carry subnet mask information along with the updates. So the disadvantage is when we use classless address there will become a problem in ruinning of classless protocol.
- Updates are broadcasted via 255.255.255.255
- Administrative distance is 120
- Max Hop Counts : 15
- Load Balancing of 4 equal paths mean that load balancing in 60-40 or 80-20
- Used for Small organization
- RIP is UDP protocol (Port 520)
- RIP has database table `show ip rip database`
- Periodic updates and Exchange entire routing for every 30 seconds but we can change them. The commend -

`show ip protocol (Checking which routing protocol is running on routers)`

![tempImageSzfTnW](https://github.com/user-attachments/assets/a6e3ec5b-7826-44b8-8614-1d83424bab9c)


- Invalid after = hold down = flushed after = 240 sec means that RIP routing status will be updated only after 240 sec even RIP protocol is down under 240 sec. But we can change by following command but **set the commend in both routers**.

`timers basic (update timer) (invalid) (holddown) (flush)` 

**RIP V2** 

- Classless routing protocol
- Supports VLSM
- Supports authentication
- Uses multicast address **224.0.0.9**

**Adv of RIP**

- Easy to Configure
- No design constraints like OSPF protocol
- No complexity
- Less overhead

**Disadv of RIP**

- Bandwidth utilization is very high as broadcast for every 30 seconds
- Works only on hop count (not consider the Bandwidth)
- Not scalable as hop count is only 15 which mean if we want to extend the network, we can't.
- Slow convergence

**Even though RIP v1 (Classfull) and RIP v2 (Classless) support auto summarization, we have to write < no auto-summary > command when we use classless in our network. If not routing will become a problem.**

----------------------------------------

**RIP Command**

`router rip`

`version 2`

`network (network-id)`

`no auto-summary`

**Checking RIP routing updates with background details processes**

`debug ip rip`

`undebug all`


**Do not distrub typing commands while debugging mode is on**

`line console 0`

`logging synchronous`


**If we do not want to send routing updates for unnecessery interface in router but still receiving routing updates**


`router rip`

`passive-interface (interface name)`


**RIP V2 Authentication**

- Configure R1 and R2 to exchange the routes only after successful authentication.

For R1 and R2

`Key Chain {Key Chain Name - Can different}`

`Key {Number - should be same}`

`Key-string {password - should be same}`

For Interfaces between each router 

`interface (interface name)`

`ip rip authentication mode md5`

`ip rip authentication key-chain (Key Chain Name)` 


***According to our lab, we set up RIP v2 routing on all routers (R1,R2,R3) and we set authentication mode on between R2 and R3. And also we set R2's ethernet 0/0 interface as passive interface. At this point R3 stills get routing updates from R1 like (10.0.0.0/30 and 192.168.1.0/24 which can be regarded as loopback and if R1 is a external untrusted network, R3 will become a problem with huge memories. To advoid this we write access-list with distributed list in R3 router which can see in the Day 2 lab picture or set R2's ethernet0/0 interface as RIP v2 authentication.***


# Day 3 (Controlling Routing Updates | Distribute List) 

<img width="935" height="272" alt="Controlling Redistribution with distribute-list" src="https://github.com/user-attachments/assets/40726cef-57a4-42dd-82a4-4a4f1c8945b1" />


**Download Lab File (EVE file)** 
[Day 3 Controlling Redistribution with distribute-list](https://github.com/Htin2001/CCNP-Enterprise/blob/678ac8f726fb874fc1ce9ed3e941493a32d56d95/Controlling%20redistribution%20with%20distribute-list.zip)

**Controlling Routing Update Traffic**

- You might need to control exactly which routes are advertised or redistributed, or which paths are chosen (Like Policy Based Routing)
- Advertised only some specific Routes to Neighbor
- Redistribute Specific Routes
- Preventing Routing loops
- Path Manipulation of some specific Routes
- Changing Metric and Metric-type fro sepcific routes
- Changing the Administrative Distance for Specific Routes
- With BGP
  - Controlling routes to be advertised to ISP
  - Control routes to get in to routing table
- Policy Based Routing


**Different types for controlling routing update traffic**

- Passive Interface
  - Passive - interface command is used in all routing protocols to **disable sending updates**      out from a specific interface. RIPv2 **doesn’t send ‘Hello packets’**
- Distribution-lists
- IP - Prefix -list
- Route - maps
- Policy Based Routing


<img width="561" height="351" alt="redistribution loops " src="https://github.com/user-attachments/assets/e3cba835-d1be-45dd-b0d4-78660d2eedf7" />



**Passive Interface in EIGRP**

- Do not send any hello messages on passive interface and then doesn’t happen neighbors and topology but still receiving routing updates
- Router ignores **any** EIGRP messages received on the passive interface
- No neighborship > No topology > No routing table if run passive interface
- EIGRP only still advertises about the connected subnets which aren't running passive interface. 

**Passive Interface in OSPF**

- In OSPF passive-interface, works just like it does with EIGRP.


**Using Distribution Lists**

- A distribute-list is used to control routing updates either
  - coming TO your router
  - or leaving FROM your router
- Distribute-lists work on a variety of **different IOS routing protocols**
- One of the easiest way
- Use with **an access list or route map or prefix-list** to permit or deny routes
- Can be applied to **transmitted, received, or redistributed routing updates**

<img width="597" height="348" alt="Filtering routing updates with a distributed list" src="https://github.com/user-attachments/assets/a23c97c9-5050-4225-94c1-43f61ec97dfe" />


<img width="1268" height="690" alt="Controlling Redistribution with Distribute lists " src="https://github.com/user-attachments/assets/085f4591-0756-45db-8236-e8ea44282cfb" />


- According to the upper Redistribution diagram, Router B is processing on redistribion which means it has both RIP and OSPF routes.
- If you want filter some routes in **OSPF** which going to RIP,the flow will
  `router rip`
  `distribution-list (no.) (in or out) ospf 1`
- If you want filter some routes in **RIP** which going to OSPF, the flow will
  `router ospf 1`
  `distribution-list (no.) (in or out) rip`
- **Before doing the distribution, we need to write the ACL (Access List) to permit or deny**
  

----------------------------------------
**Running Passive Interface in EIGRP**

`router EIGRP 100` 

`passive-interface (interface name)`

`debug eigrp packet` (Checking eigrp packets)

**Running Passive Interface in OSPF**

`router OSPF 1`

`passive-interface (interface name)`

`debug ip ospf events` (Checking ospf packets)

**If you want to close all physical and logical interfaces in router, the command is** 

`router rip` 

`passive-interface default`

`no passive-interface (interface-name)` 

**Filtering Routing updates with a Distribute List** 

- At this point we need to use distribute-list with ACL

`access-list (no.) (permit or deny) (network-id) (wildcard mask)`

`distribute-list (access-list's no.) (in or out) (interface-name)`


# Day 4 (Prefix-List | Offset-list | VRF Lite)

**Prefix-list**

<img width="997" height="315" alt="prefix-list" src="https://github.com/user-attachments/assets/d829da9c-f1be-40d3-a93e-def7dc1923e1" />


**Download prefix-list lab file (EVE file)**

[Day 4 prefix-list lab](https://github.com/Htin2001/CCNP-Enterprise/blob/b0f96b534eb3e8c68b56914cf7853fd18aa28ffc/Prefix-list%20lab.zip)

- In this lab, we only allow to enter ( 10.0.0.0 / 18 - 20 ) in Nancy's router from Karen. We ran prefix-list command in Nancy's router. 


- Prefix-list is used when it is difficult to write with the access-list. If we need to write 5    access-list commands, we only need to write only **one or two commands** when we use    prefix-list
- The IOS IP prefix-list another tool for matching routes
- The command then sets either a deny or permit action for each matched prefix / length
- Match **two components** of an IP route

  - The route prefix (the subnet number i.e. 192.168.0.0)
  - The prefix length (the subnet mask i.e. /24)
  
    - 10.0.0.0 /8 (Only that network)
    - 10.0.0.0 /8 ge 9 (i.e. 10. x . x . x / 9-30)
    - 10.0.0.0 /8 ge 24 le 24 (only /24 network)
    - 10.0.0.0 /8 le 28 (i.e 10. x . x . x / 8-28)
    - 0.0.0.0 /0 (Default route)
    - 0.0.0.0 /0 le 32 (permit any)
    - **10.0.0.0/16 le 16 (10 . 0 . x . x / le 16)**


**Offset-list**

<img width="1156" height="402" alt="Offset-list" src="https://github.com/user-attachments/assets/1d68af06-1c12-4863-857b-04a0711946d7" />


**Download offset-list Lab (EVE file)** 

[Day 4 Offest-list Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/5cc5674e864c9d6803c41faeec99c7a3bd3c619d/Offset-list.zip)

  - According to the picture, traffic from R1 (source router) to Loopback 1, Loopback 2, and Loopback 3 (destination networks: 13.0.0.0/24, 13.1.0.0/24, and 13.2.0.0/24) normally passes through the 11.0.0.0/30,      11.0.0.4/30, and 12.0.0.0/30 networks because the upper path has a lower hop count and is therefore preferred by RIP. However, in this lab, an offset list is configured to increase the hop count of routes        learned via the 11.x.x.x networks, making the upper serial path less preferred. As a result, traffic is forced to use the lower path through the 10.x.x.x networks. Additionally, the lower path uses Ethernet      links, which provide higher bandwidth and lower latency compared to the upper serial links, making it a more desirable path from a performance perspective despite having more hops. 

- An offset-list is a way to increase the metric of routes
- Only **RIP and EIGRP** support offset-list
- One thing we have to note that the best route will be chosen over the **lowest metric (RIP) or delay value (EIGRP)**
- Offest-list only works with access-list


**VRF Lite (Virtual Routing and Forwarding)** 

![VRF Blue](https://github.com/user-attachments/assets/54eec78a-9da4-4967-b693-7d1b1405cadf)


![VRF Red](https://github.com/user-attachments/assets/72291cfe-4fd4-44a6-92e5-f19fc7bfff50)

**Download VRF with OSPF route lab file (EVE file)**

[Day 4 VRF with OSPF route lab file](https://github.com/Htin2001/CCNP-Enterprise/blob/8507f9eabd94c76244b3e256d5b9d0230e7ae2db/VRF%20Lab.zip)


<img width="385" height="267" alt="without vrf" src="https://github.com/user-attachments/assets/d10d6cfe-4b29-4c13-a109-f17d9e8a448a" />

<img width="376" height="249" alt="with vrf" src="https://github.com/user-attachments/assets/f20b247a-b1c1-48f2-a85a-e7e59e938f7b" />

- Virtual routing and forwarding is a technology that creates separate virtual routers on a physical **router** which means there will become   a separate routing table which is also like    VLAN.
- VRFs are commonly used for MPLS deployments (Layer 3 VPN), when we use VRFs without MPLS then we call it VRF lite.



----------------------------------------
**Prefix-list Command** 

`ip prefix-list (name) (seq no.) (permit or deny) (network with subnet or two components)` 

`sh ip prefix-list {checking prefix-list}`


**Matching prefix-list with distribute-list** 

`router rip or eigrp 100` 

`distribute-list prefix (prefix-list's name) (in or out)` 


**Offset-list Command**

- When we use the offset-list, we have to use it with access-list. So, we need to set the          access-list first.

`ip access-list (standard or extended) (name)`

`permit (destination's network) (destination's wildcard mask)`

`router rip or eigrp 100`

`offset-list (access-list's name) in (numbers) (interface name)` 


**Creating VRF tables**

`ip vrf (name)` 

`sh ip vrf {verifying vrf}`


**Assign VRF tables to intterfaces**

`int (int-name)` 

`ip vrf forwarding (vrf_name)` 
- If you assigned ip address in global routing table, we need to assign ip address for that interface because of enabling VRF.

`show ip route vrf (vrf_name) {checking vrf routing table}`


**Routing OSPF route with VRF** 

`router ospf 1 vrf (vrf_name)
...............................` 

`router ospf 2 vrf (vrf_name)
...............................` 

- Noted that when we run the ospf, we need to use different ospf id for different vrfs.

`show ip route vrf (vrf_name) ospf {Verifying OSPF route with VRF}`



# Day 5 (Route Map) 


**Route Map** 

- They offer top-down processing
- Lines are sequence-numbered for easier editing
- Route maps are named rather than numbered for easier documentation
- Match criteria and set criteria can be used, similar to the “if, then” logic in a scripting language
- **Match** criteria used with **ACL & Prefix-list** while **set** criteria used with **action**
- The common uses of route-maps are as follows:
  - Redistribution route filtering
 
    **Download Redistribution Using Route-Map Lab File (EVE File)**
    
    [Day 5 Redistribution Using Route-Map Lab File](https://github.com/Htin2001/CCNP-Enterprise/blob/09bbf55837d35935e3da6f9600d304445b8f889e/Redistribution%20Using%20Route-Map.zip) 
    <img width="902" height="447" alt="image" src="https://github.com/user-attachments/assets/7387e645-418d-43de-8595-9d5b1bd01030" />
    - According to the image, RIP routes entering into OSPF routes, we need to make ***Redistribution RIP routes*** into OSPF routes in router R3.
    
    - `router ospf 1`
   
    - `no redistribute rip subnets`
   
    - `redistribute rip subnets route-map (route-map's name)`

  - Policy-based routing

    **Download PBR Lab File (EVE File)**
 
    [Day 5 PBR Lab File](https://github.com/Htin2001/CCNP-Enterprise/blob/aa81e545546016f4eb6da00c7faa74870f3627dd/Day%205%20PBR%20Lab.zip) 

    <img width="696" height="472" alt="image" src="https://github.com/user-attachments/assets/19e58a7c-a95b-4813-933f-94adac9a4ee4" />

      - According to the image, PBR need to assign the incoming interface of router R1. So, in R1 we need to write
      
      - `int e0/0`
     
      - `ip policy route-map (route-map's name)`
    
    - It is used for implementing policy that cause the packet to take a different direction
    - PBR allows **source** based routing
    - Routing table is destination base
      - For advantages;
        - Different users can go from different directions
        - Load sharing
        - PBR will implemented on the **incoming direction of the source interface**
        - If the packet is match in the route map and it is permit it will be send according to the policy which means that **route-map uses only with permit action**
        - If the packet is match in the route map and route map deny packet will be forwarded according to normal routing table

  
  - BGP policy implementation


----------------------------------------
**Route-Map Command**

`route-map (name) permit (route-map's sequential number)`

`match ip add (access-list's number)`

`set (.....)` 

- (....) has `metric (metric number)` / `ip next hop (next-hop ip)` / `interface (interface_name)`

**Checking route-map** 

`sh route-map (route-map name)` 

**Checking PBR with debug** 

`debug ip policy` 

**Checking interface excluding unassigned interfaces**

`sh ip interface brief | exclude unassigned`



# Day 6/7 (OSPF)

- Link-state routing protocol
- Open standard
- SPF or Dijkstra algorithm
- Classless
- Supports FLSM, VLSM, CIDR and Manual summary
- Incremental updates (hello = 10 sec dead = 40sec) which means that updates are happened during changes
- Updates are sent as multicast (224.0.0.5 & 224.0.0.6) 
- Metric = Cost (Reference Bandwidth / Interface Bandwidth)
 
    - Cost = Reference BW / Interface BW 
 
    - Reference BW = 100
    
    - Ethernet Cost = 100 / 10 = 10
    
    - Fastethernet Cost = 100 / 100 = 1
    
    - Gigabitethernet Cost = 100 / 1000 = 1
    
      - **Noted that if there are two fastethernet and gigabitethernet, we need to config Reference BW in interface in OSPF**
    
- Administrative distance = 10
- Load balancing via 4 equal cost paths by default (unequal cost load balancing not supported)
- Hierarchical network design using Areas
- Using RTP (Reliable Transport Protocol : 89)
- All the routers must have the same database


<details>
<summary><strong>OSPF 7 Stages</strong></summary>

<img width="581" height="687" alt="image" src="https://github.com/user-attachments/assets/77b0e4df-e1b2-4394-8588-82e53a369769" />


<details>
<summary>Down</summary>

- Situation before nothing happen
- Situation become happening down state after happening full state

</details>

<details>
<summary>INIT</summary>
 
- Situation sending (Hello Packets)

<img width="300" height="400" alt="image" src="https://github.com/user-attachments/assets/57eab74f-b1f9-4c89-8af1-9b3359e25c1b" />

- According to the image, * should be the same when sending (Hello Packets)
- Sending Hello packets with multicast address (**224.0.0.5 or 224.0.0.6**) from source to destination
- Receiving Hello packets with unicast address from destination to source

</details>

<details>
<summary>2-Way</summary>

- Situation of happening Neighbors

</details>


<details>
<summary>Exstart</summary>

<img width="350" height="100" alt="image" src="https://github.com/user-attachments/assets/399f98fc-7e39-4264-b45d-5478bb869e74" />


- Start Exchange Router-ID stored in DBD (Database description)

</details>


<details>
<summary>Exchange</summary>

<img width="300" height="153" alt="image" src="https://github.com/user-attachments/assets/c941d0ab-de4d-472a-80d9-3eedc1304a2b" />

- Start exchange LSDB (Link-state database) and then exchange LSAck 

</details>


<details>
<summary>Loading</summary>

<img width="626" height="98" alt="image" src="https://github.com/user-attachments/assets/322d8569-2855-4751-b19f-6e8aadda8b95" />

- Start Exchange LSR (Link-state Request)

</details>


<details>
<summary>Full</summary>

<img width="582" height="191" alt="image" src="https://github.com/user-attachments/assets/51196cac-7214-45d6-9257-c2b699986cee" />

- State of LSAck

</details>

</details>


<details> 
<summary> <strong> OSPF Packet Type</strong></summary>

- Incremental Update ! 

<details>
<summary>Hello</summary>



</details>

<details>
<summary>LSA (Link State Advertisement) = Update Packet</summary>

<img width="606" height="507" alt="image" src="https://github.com/user-attachments/assets/098f9ce0-40af-4a17-81c3-2e48409753ca" />

The aging timer of LSAs is 39 mins 

- Type 1 - Router LSAs (Updates in same areas)
- Type 2 - Network LSAs (having DR & BDR)
- Type 3 - Summary LSAs (Updates from different areas)
- Type 4 - Summary ASBR LSAs (LSAs which describe ASBR location)
- Type 5 - Autonomous system external LSAs (Redistributed Routes)
- Type 6 - Multicast OSPF LSA
- Type 7 - Special Area or Stub area (Define for not-so-stubby areas)
- Type 8 - External attributes LSA for BGP
- Type 9 / 10 / 11 - Opaque LSAs


**OSPF Routing Protocol Code**

- check with

`show ip route ospf` 

- O = Intra area route (LSA Type 1)
- OIA = Inter area route (LSA Type 3)
- OE1 = External Type 1 route (LSA Type 5)
- OE2 = External Type 2 route (LSA Type 5)
- ON1 = NSSA Type 1 route (LSA Type 7)
- ON2 = NSSA Type 2 route (LSA Type 7)
- (* which can be O*IA, O*, O*E1, O*E2, O*N1, O*N2) = Default Route

</details>


<details>
<summary>DBD (Database Description)</summary>



</details>


<details>
<summary>LSR (Link State Request)</summary>



</details>


<details>
<summary>LSU (Link State Update)</summary>



</details>


<details>
<summary>LSACK (Link State Acknowledgement</summary>



</details>

</details>

<details> 
<summary><strong> OSPF Table Type</strong></summary>

**All routers must have the same database  within same area** 
- Neighbor Table
- Database Table (having LSAs)
- Routing Table

</details> 

<details> 
<summary><strong> OSPF Router Role </strong></summary>

<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/97427e75-e744-4030-a5fc-2d36b76f3e92" />

- Area Router (Router located inside one area which doesn’t hit any areas)
- Area Border Router - ABR (Router hitting at least 2 areas)
- Autonomous System Border Router - ASBR (Router hitting other routing protocol)

</details> 

<details> 
<summary> <strong> OSPF Network Type </strong></summary>
 
- Broadcast Multi Access Network (Hello = 10 , Dead = 40) (DR/BDR election)
  
  <img width="300" height="250" alt="image" src="https://github.com/user-attachments/assets/126d7d97-e8cd-47ae-9461-da4e108ca63c" />

  - LAN technologies like Ethernet and Token Ring
  - DR and BDR selection are required
  - OSPF detects this type of link automatically
  - All neighbor routers form full adjacencies with the DR and BDR only which means that DR_others don't make neighbor by themself

- Point to Point (Hello = 10 , Dead = 40)
- NBMA (Hello = 30 , Dead = 120) (DR/BDR election)
- Point to multipoint (Hello = 30 , Dead = 120)
- Point to multipoint non broadcast (Hello = 30 , Dead = 120)

- In OSPF, define Network Type depending on the using of interface/Cable like :
    - Broadcast (Ethernet Interface = E0/0, fa0/0, Gi0/0, TenG0/0,……)
    - Point to point (Serial Interface)
 
</details>

<details> 
<summary> <strong> DR / BDR Election  </strong></summary>
 
- Reducing neighbor numbers flooding in one OSPF area

<img width="250" height="200" alt="image" src="https://github.com/user-attachments/assets/a52bc923-d5f6-4422-a689-6b110fd1b08a" />

- There are 56 neighbors

<img width="250" height="200" alt="image" src="https://github.com/user-attachments/assets/487ad22a-ca93-42ea-bc71-b7de3978eec9" />

- After that there will be 14 neightbor in total

<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/c688a9ba-3670-4b50-9589-df15483fd8d9" />

- Closing DR / BDR on both routers according to the following image
  - Priority 0
  - P2P
- DR others to DR is using multicast address (224.0.0.6)
- DR to DR others is using multicast address (224.0.0.5)
- The router having highest priority is DR (0 - 255)
    - Checking inside the neighbor table

    `sh ip ospf neighbor` 
      
- The router with second-highest priority is BDR
- The default priority value is 1
- In the case of a tie, router with highest router ID is DR second highest router ID becomes the BDR
  - Router ID is used to identify each router Router-ID
    - Manual Router-ID
    - Highest IP of the logical interface (if configured)
    - Highest IP address of Active Physical Interface
- If router priority is 0 it cannot become the DR or BDR
- Router which is not a DR or BDR is called as DROTHER
- DR & BDR election is not preemptive which means that when DR is down > BDR becomes DR. When DR is up > BDR still left as DR.
- Changing priority

`(intface_name)`

`ip ospf priority (0-255)` 

- DR / BDR Elections Neighbors
    - DR / BDR > DROTHER > Full
    - DROTHER > DR / BDR > Full
    - DROTHER > DROTHER > 2-Way
- Updates
    - DROTHER > DR / BDR > 224.0.0.6
    - DR > DROTHER > 224.0.0.5

</details>


**Sequence Timer in LSAs** 

- They are 4 bytes or 32 bytes
- Start with 0x80000001 and End with 0x7FFFFFFF

----------------------------------------
**OSPF Running** 

`router ospf (process_id)` 

<process-id should be same within same area while process-id shouldn't be the same in ABR> 

`network (network_ip) (wildcard_mask) (area_number)` 



**Checking network-ip with route** 

`show ip route (network_ip)` 




**Checking route with interface** 

`show ip ospf (intface_name)` 



**Changing metric cost for interface** 

`ip ospf cost (number) <number can between 1 and 65535>` 



**Checking OSPF Neighbor process** 

`debug ip ospf adj` 

or 

`debug ip ospf events` 



**Clear ospf process**

`clear ip ospf process`



# Day 8 (OSPF Virtual Link | Summarization)


<img width="600" height="630" alt="image" src="https://github.com/user-attachments/assets/2d16aebd-fbeb-4638-a900-13b4d6b4d798" />


- Virtual links are used to connect a dis-contiguous area  to area 0
- A logical connection is built between routers (ABR)
- Virtual links are recommended for backup or temporary connections

**Virtual-link Two Vritual Transit Areas**

<img width="926" height="572" alt="image" src="https://github.com/user-attachments/assets/1ae181ad-1f32-4798-90a0-6434503ec55f" />


----------------------------------------
**Configuration of OSPF Virtual-link**

`router ospf (process-id)` 

`area (area-id) virtual-link (router-id)` 

- area-id means transit area
- router-id means remote ABR router-id 






