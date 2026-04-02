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
- Changing Metric and Metric-type for sepcific routes
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

**Cisco EVN (Easy Virtual Network)**

- Like Making Trunk 

<img width="500" height="195" alt="image" src="https://github.com/user-attachments/assets/0f1976d7-c2f7-4def-a159-2f624777b44d" />

**VRF Lite Route Leaking**

<img width="800" height="357" alt="image" src="https://github.com/user-attachments/assets/da526a1a-5369-4017-a80e-961fc7d3f80b" />

- Like Inter_Vlan Routing
- Different VRFs Communication
- It is possible to **leak** route from one VRF into another. There are two options to achieve this
  - Static Routes
    - Static Routes directly between VRFS are not supported. What does work, is routing traffic from a VRF to the global routing table and then to the destination VRF
      <img width="900" height="545" alt="image" src="https://github.com/user-attachments/assets/7c627cfc-e916-485a-8cd8-6cdd11d3e220" />

      <img width="900" height="558" alt="image" src="https://github.com/user-attachments/assets/bafb129c-2494-4c8a-9330-b0e8f4bb828c" />
  - MP-BGP


----------------------------------------
**Prefix-list Command** 

`ip prefix-list (name) (seq no.) (permit or deny) (network with subnet or two components)` 

`sh ip prefix-list {checking prefix-list}`


**Matching prefix-list with distribute-list** 

`router rip or eigrp 100` 

`distribute-list prefix (prefix-list's name) (in or out)` 


**Offset-list Command**

- When we use the offset-list, we have to use it with access-list. So, we need to set the access-list first.

`ip access-list (standard or extended) (name)`

`permit (destination's network) (destination's wildcard mask)`

`router rip or eigrp 100`

`offset-list (access-list's name) in (numbers) (interface name)` 


**Creating VRF_lite tables**

`ip vrf (name)` 

`sh ip vrf {verifying vrf}`

**Creating EVN**

`vrf definition (vrf_name)` 

`vnet tag (number)`

- number shouldn't be the same for different vrfs
- Vnet tag numbers work like Vlan tag. For example {Data+Vnet_Tag_Number} through other side routers

`address-family ipv4`

**Assign VRF tables to interfaces in VRFS**

`int (int-name)` 

`ip vrf forwarding (vrf_name)` 

`ip address (network) (subnet mask)`

- If you assigned ip address in global routing table, we need to assign ip address for that interface because of enabling VRF.

**Assign VRF tables to interfaces in EVN Configuration**

`int (int-name)` 

`vrf forwarding (vrf_name)` 

`ip address (network) (subnet mask)`

**Checking vrf-lite routing table** 

`show ip route vrf (vrf_name) `

**Checking vrf for EVN**

`show vrf` 

**Make Trunk in EVN**

`interface (interface_name)` 

`vnet trunk`

`ip address (network) (subnet mask)` 

**Routing OSPF route with VRF** 

`router ospf 1 vrf (vrf_name)
...............................` 

`router ospf 2 vrf (vrf_name)
...............................` 

- Noted that when we run the ospf, we need to use different ospf id for different vrfs.

**Verifying OSPF route with VRF**

`show ip route vrf (vrf_name) ospf `

**Checking interfaces in details** 

`show derived-config | begin (interface_name)` 

**Routing Context** 

`routing context vrf (vrf_name)` 

- Do not need further to type vrf like

  `show ip route ospf`



# Day 5 (Route Map) 


**Route Map** 

- They offer top-down processing
- Lines are sequence-numbered for easier editing
- Route maps are named rather than numbered for easier documentation
- Match criteria and set criteria can be used, similar to the “if, then” logic in a scripting language
- **Match** criteria used with **ACL & Prefix-list** while **set** criteria used with **action**
- The common uses of route-maps are as follows:

**Redistribution route filtering**

<img width="902" height="447" alt="image" src="https://github.com/user-attachments/assets/7387e645-418d-43de-8595-9d5b1bd01030" />

**Download Redistribution Using Route-Map Lab File (EVE File)**
    
[Day 5 Redistribution Using Route-Map Lab File](https://github.com/Htin2001/CCNP-Enterprise/blob/09bbf55837d35935e3da6f9600d304445b8f889e/Redistribution%20Using%20Route-Map.zip) 
    
- According to the image, RIP routes entering into OSPF routes, we need to make ***Redistribution RIP routes*** into OSPF routes in router R3.
    
- `router ospf 1`
   
- `no redistribute rip subnets`
   
- `redistribute rip subnets route-map (route-map's name)`

**Policy-based routing**

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

  
 **BGP policy implementation**


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



# Day 6/7/9 (OSPF)

<img width="700" height="760" alt="image" src="https://github.com/user-attachments/assets/1006c76b-038b-42c7-8f0a-9b4b5609b449" />


[Day 9 OSPF _ Stub - Totally Stub - NSSA - Totally NSSA EVE Lab File](https://github.com/Htin2001/CCNP-Enterprise/blob/27060046dbcdaa16051fb25ae9bc6717361fb1fc/Day%209%20OSPF%20_%20Stub%20-%20Totally%20Stub%20-%20NSSA%20-%20Totally%20NSSA.zip)


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

- **Type 1 - Router LSAs (Updates in same areas)**
  
  <img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/177a4c49-cd46-4258-a0bf-2782f2bb4f2e" />
  
  - generated by the internal router (same area)
    
  - “O” routes in the routing table
    
  - Floods within its area only: does not cross ABR

    
- **Type 2 - Network LSAs (having DR & BDR)**

  <img width="500" height="330" alt="image" src="https://github.com/user-attachments/assets/1de4bf7a-2c44-4764-99ec-8fbc41187934" />

  - Advertised by the DR of the transit network (DR > other (LSA2))

  - Floods within its area only; doesn’t cross ABR

  - “O” routes

  - happens in broadcast networks

- **Type 3 - Summary LSAs (Updates from different areas)**

  <img width="500" height="320" alt="image" src="https://github.com/user-attachments/assets/1122796f-bbb9-4e54-bc4a-c0f64377c793" />

  - sending updates from one area to another area (”OIA” routes in the routing table)

  - Type 3 LSAs are used to flood network information to areas outside the originating area (inter area)

  - Advertised by the **ABR** of originating area

  - Regenerated by subsequent ABRs (ABR2) to flood throughout the autonomous system
  
- **Type 4 - Summary ASBR LSAs (LSAs which describe ASBR location)**

  <img width="500" height="402" alt="image" src="https://github.com/user-attachments/assets/ed8b2aa6-49a4-440a-901b-938bd2806f83" />

  - Summary (type 4) LSAs are used to advertise an ASBR to all other areas in the autonomous system

  - **They are generated by the ABR of the originating area**

  - They are regenerated by all subsequent ABRs to flood throughout the autonomous system

  - Type 4 LSAs contain the router ID of the ASBR (only can check in OSPF database table)

- **Type 5 - Autonomous system external LSAs (Redistributed Routes)**

  <img width="500" height="394" alt="image" src="https://github.com/user-attachments/assets/9c90eb3c-47d9-4f11-958c-57e4bcaa1318" />

  - External routes redistributed in to OSPF ( LSA 5 )

  - **Type 5 LSAs are advertised and owned by the originating ASBR**

  - Type 5 LSAs flood throughout the entire autonomous system

  - The advertising router ID (ASBR) is unchanged throughout the autonomous system

  - Type 4 LSA is needed to find the ASBR
  
- **Type 6 - Multicast OSPF LSA**

  - Used in multicast routing (MOSPF routing protocol)

  - Multicast LSA (Cisco routers don’t support)


- **Type 7 - Special Area or Stub area (Define for not-so-stubby areas)**

  <img width="600" height="462" alt="image" src="https://github.com/user-attachments/assets/c56d13cf-f641-41d6-9d01-c612319f3803" />

  - Advertises routes in another routing domain, generated by an ASBR

  - Within a not-so-stubby area

     - N1 - metric increases as it is passed through the network
       
     - N2 - metric does not increase (default)


  
- **Type 8 - External attributes LSA for BGP**

  - Used in OSPF and BGP interworking
  
- **Type 9 / 10 / 11 - Opaque LSAs**

  - Used for specific applications, such as OSPF and MPLS interworking


**OSPF Routing Protocol Code**

- check with

`show ip route ospf` 

- O = Intra area route (LSA Type 1)
- OIA = Inter area route (LSA Type 3)

<img width="600" height="552" alt="image" src="https://github.com/user-attachments/assets/b08a05e8-e322-408a-8e92-4b388e37bf5b" />

- OE1 = External Type 1 route (LSA Type 5)

    - OE 1
      
        - Will add the individual metric
          
        - It changes as move from router to router
          
- OE2 = External Type 2 route (LSA Type 5)

     - OE 2
       
        - Will not add the individual metric
          
        - It remains same metric for all the routers
          
        - Default for external routes
          
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


**OSPF Stubs**

- OSPF stubs allows the routers in an area to use default routes for forwarding packets to ABRs, rather than more specific routes
- Reduces memory consumption and CPU processing time on the routers inside the area, because the routers in that area can have fewer LSAs in their LSDBs

**1. Stub ( Filter Type 5 )**

- External LSAs are stopped (OE1 and OE2 routes)
  
- Default route is advertised into stub area by the ABR
  
- All routers in stub area must be configured as stub
  
- Can’t run on Area 0
  
- Can’t accept for ASBR
  
- No virtual links must pass through the area which means can’t be transit areas
  
- All routers in the area are configured as stub routers

<ins>Before Stub</ins> 

<img width="800" height="835" alt="Day 9 Original" src="https://github.com/user-attachments/assets/e58c628c-bf2f-4ee2-ae54-a9eff6a03f78" />

<ins>After Stub</ins> 

<img width="800" height="821" alt="Day 9 Stub" src="https://github.com/user-attachments/assets/5da5b7b7-a817-45ef-a8bb-53d2125c1a01" />


**2. Totally Stub (Type 3 and 5)**

- External LSAs are stopped (E1 and E2)
  
- Summary LSAs are stopped (OIA routes)
  
- Routing table is reduced to a minimum
  
- This is a Cisco proprietary feature

<ins>Before Totally Stub</ins> 

<img width="800" height="835" alt="Day 9 Original" src="https://github.com/user-attachments/assets/e58c628c-bf2f-4ee2-ae54-a9eff6a03f78" />

<ins>After Totally Stub</ins> 

<img width="800" height="837" alt="Day 9 Totally Stub" src="https://github.com/user-attachments/assets/c2de1fc4-7d18-4d38-9225-ec87a565af0a" />




**3. NSSA (Change into Type 7 from Type 5)**

- NSSA breaks stub area rules
  
- ASBR (R1) is allowed in NSSA
  
- Special LSA type 7 defined, sent by ASBR
  
- ABR converts LSA type 7 to LSA type 5
  
- ABR sends default route into NSSA instead of external routes from other ASBRs
  
- NSSA is an RFC addendum

<ins>Before NSSA</ins> 

<img width="800" height="775" alt="Day 9 Org 1" src="https://github.com/user-attachments/assets/50592f1a-4b3f-43f1-8319-f2b45bb8c7ac" />

<ins>After NSSA</ins> 

<img width="877" height="778" alt="Day 9 Nssa" src="https://github.com/user-attachments/assets/5b115587-0653-47f9-a74b-a1997faad70f" />


**4. Totally NSSA (Filter Type 3, and Change into Type 7 from Type 5)**

<ins>Before Totally NSSA</ins>

<img width="800" height="775" alt="Day 9 Org 1" src="https://github.com/user-attachments/assets/50592f1a-4b3f-43f1-8319-f2b45bb8c7ac" />


<ins>After Totally NSSA</ins> 

<img width="847" height="786" alt="Day 9 Totally Nssa" src="https://github.com/user-attachments/assets/3b028006-5af7-47f4-9d6b-5895da01d1e4" />


----------------------------------------
**OSPF Running** 

`router ospf (process_id)` 

<process-id should be same within same area while process-id shouldn't be the same in ABR> 

`network (network_ip) (wildcard_mask) (area_number)` 

**OSPF Stub Area Configuring** 

<img width="400" height="613" alt="image" src="https://github.com/user-attachments/assets/5876afb8-feff-4cde-8517-df3eba07c1f1" />

`On all routers of Area 10`

`Rx# router ospf 1`

`Rx# area 10 stub` 

**OSPF Totally Stub Area Configuring** 

<img width="400" height="613" alt="image" src="https://github.com/user-attachments/assets/5876afb8-feff-4cde-8517-df3eba07c1f1" />

All routers of Area excluding ABR (R1 / R2)

`router ospf 1`

`area 10 stub`

Only on ABR (R3)

`router ospf 1`

`area 10 stub no-summary`

**OSPF NSSA Configuring** 

<img width="400" height="716" alt="image" src="https://github.com/user-attachments/assets/83b649e9-2b8f-4803-b5df-60ce88601f7f" />


All Internal routers (R1 / R2) 

`router ospf 1`

`area 10 nssa`

On ABR (R3) 

`router ospf 1`

`area 10 nssa default-information-originate`


**OSPF Totally NSSA Configuration**

<img width="400" height="716" alt="image" src="https://github.com/user-attachments/assets/af7bf1b2-5c9e-4736-ab51-f4daf55afb4b" />


All Internal routers (R1 / R2) 

`router ospf 1`

`area 10 nssa`

On ABR (R3) 

`router ospf 1`

`area 10 nssa no-summary`


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


**Checking OSPF LSA Types**

`sh ip ospf database` 


**Clear ospf process**

`clear ip ospf process`



# Day 8 (OSPF Virtual Link | Summarization)

**OSPF Virtual-link**

<img width="600" height="630" alt="image" src="https://github.com/user-attachments/assets/2d16aebd-fbeb-4638-a900-13b4d6b4d798" />


- Virtual links are used to connect a dis-contiguous area  to area 0
- A logical connection is built between routers (ABR)
- Virtual links are recommended for backup or temporary connections

**Virtual-link Two Vritual Transit Areas**

<img width="926" height="572" alt="image" src="https://github.com/user-attachments/assets/1ae181ad-1f32-4798-90a0-6434503ec55f" />

**Download Virtual-link two virtual transit areas Lab File (EVE File)**

[Day 8 Two Virtual Transit Areas Virtual-link Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/9b9da220ffa54418e081ac801933882028f1ca0b/Day%208%20OSPF%20Virtual%20Link.zip)


- According the upper two virtual transit areas lab, we need to type command like following
> R2# router ospf 1
>
> R2# area 30 virtual-link 6.6.6.6
>
> R2# area 20 vritual-link 5.5.5.5

> R6# router ospf 1
>
> R6# area 30 virtual-link 2.2.2.2

> R5# router ospf 1
>
> R5# area 20 virtual-link 2.2.2.2


**Summarization**


- It is the processes of combining smaller networks in to single large sub network (Combining the contagious address into one and send to neighbor)
- It helps in reducing the size of routing table 

<img width="1000" height="631" alt="image" src="https://github.com/user-attachments/assets/bbe33608-29c9-4e19-9a5b-256a8b477355" />

**Download RIP / EIGRP / OSPF Route Summarization Lab (EVE Lab File)** 

[Day 8 RIP / EIGRP / OSPF Route Summarization Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/f7dcc27fd92cf74f52313eba5e1159e84d9430a6/Day%208%20Summarization.zip)

**Summarization Adv**

- Minimizing the routing table
- Less use of resources like memory, processor, bandwidth

**Summarization Disadv**

- Even though it reduces number of routes, it includes unnecessary networks
- Router which accepts summarization, it doesn’t know which networks are used by the neighbor routers

**Auto Summary**

- Summarization is done to a default class full boundary ( /8 /16 /24 )
- Routing protocol like RIPv2, EIGRP, BGPv4 support auto summary and can disabled
- Routing protocol like OSPF and ISIS doesn’t support auto summary

**Manual Summary**

- Administrator manually configures Summarization to specific boundary
- It is supported by all classless routing protocols
- EIGRP, RIPv2, OSPF, BGP support Manual summary

<img width="648" height="461" alt="image" src="https://github.com/user-attachments/assets/2d0dbd4e-29a2-435d-bc70-0ed99c9d5e1e" />

<img width="1000" height="708" alt="image" src="https://github.com/user-attachments/assets/0acd1a44-8f48-42f2-81c1-553f79299144" />

<img width="1000" height="822" alt="image" src="https://github.com/user-attachments/assets/fc37103f-f670-46b0-a4ba-c336f9254248" />

<img width="790" height="506" alt="image" src="https://github.com/user-attachments/assets/e43e0dd2-2712-4564-b492-5426674fe74c" />


----------------------------------------
**Configuration of OSPF Virtual-link**

`router ospf (process-id)` 

`area (area-id) virtual-link (router-id)` 

- area-id means transit area
- router-id means remote ABR router-id 


**RIP Summarization**

`interface (interface_name)`

`ip summary address rip (network_id) (subnet_mask)` 

**EIGRP Summarization**

`interface (interface_name)`

`ip summary address eigrp (process_number) (network_id) (subnet_mask)`


**OSPF Summarization**

- OSPF routes on ABR

`router ospf (process_id)`

`area (area_number which desired summarization networks are located) range (network_ip) (subnet_mask)` 


- External routes on ASBR

`router ospf (process_id)`

`summary-address (network_ip) (subnet_mask)` 



# Day 10 (VLAN)

**VLAN**

- VLAN divides broadcast domains virtually
  
- VLANs are defined in the IEEE 802.1Q standard, which states that 32 bits are added to the packet header in the following fields:

     <img width="800" height="246" alt="image" src="https://github.com/user-attachments/assets/dbc036f2-3693-42ef-b132-1ce42ff1e400" />

    - **VLANS headers are not added to packets as they are forwarded locally in the switch. VLAN headers are added for packets that are sent across         on trunk ports** 
  
    - Tag protocol identifier (TPID): This 16-bit field is set to 0x8100 to identify the packet as an 802.1Q packet
      
    - Priority code point (PCP): This 3-bit field indicates a class of service (CoS) as part of Layer 2 quality of service (QoS) between switches
      
    - Drop eligible indicator (DEI): This 1-bit field indicates whether the packet can be dropped when there is bandwidth contention
      
    - VLAN identifier (VLAN ID): This 12-bit field specifies the VLAN associated with a network packet
 
 - The VLAN identifier has only 12 bits, which provide 4094 unique VLANs. Catalyst switches use the following logic for VLAN identifiers:

    - VLAN 0 is reserved for 802.1p traffic and cannot be modified or deleted
      
    - VLAN 1 is the default VLAN and cannot be modified or deleted
      
    - VLANs 2 to 1001 are in the normal VLAN range and can be added, deleted, or modified as necessary
      
    - VLANs 1002 to 1005 are reserved and cannot be deleted
      
    - VLANs 1006 to 4094 are in the extended VLAN range and can be added, deleted, or modified as necessary


**Trunk Port**

- Must be same vlan_ids on every single switch

- Passing same VLAN Traffic between switches using Single Link

- When VLAN passes through on trunk link, it taged with the trunking protocols

  - Responsible for adding and removing tags on trunk links
    1. ISL (Inter-switch Link) [ Cisco Proprietary ] 
   
    2. IEEE 802.1Q [ IEEE Open Standard ] 


----------------------------------------

**Creating VLANs**

`vlan (number) or vlan 10 - 20 <range>` 

`name (vlan_name)` 

**Assign ports into vlan**

`interface (interface_name)`

`switchport mode access`

`switchport access vlan (vlan no.) / or / name (Vlan_name)` 

**Verify VLANs** 

`show vlan brief` 

`show vlan summary` 

`show vlan id (vlan-id)` 

`show (vlan_name)`

**Trunk Configuration**

`interface (interface_name)` 

`switchport trunk encapsulation dot1q`

`switchport mode trunk`

**Limit VLANS on Trunk Links**

`switchport trunk allowed vlan (vlan no.)` 

**Verify Trunk** 

`show interface trunk` 


# Day 11 (VTP | Dynamic Trunking Protocol | Etherchannel)

**VLAN Trunking Protocol**

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/a7c9aa46-b471-4fa7-8dc6-0d5b40eb4447" />

**Download VTP Configuration Lab File (EVE)**

[Day 11 VTP Configuration](https://github.com/Htin2001/CCNP-Enterprise/blob/898fed9164e4b0333d83231bd8019452b72f31fe/Day%2011%20VTP.zip)

- VLAN Trunking Protocol (VTP) to reduce the burden of provisioning VLANs on switches
  
- There are four roles in the VTP architecture
  
  - Server
  - Client
  - Transparent
  - Off

- VTP Versions 1 and 2 limited propagation to VLANs numbered 1 to 1005. VTP Version 3 allows for the full range of VLANs 1 to 4094

- VTP supports having multiple VTP servers in a domain. These servers process updates from other VTP servers just as a client does. If a VTP domain     is Version 3, the primary VTP server must be set with the executive command vtp primary.


**VTP Communication**

- VTP advertises updates by using a multicast address across the trunk links for advertising updates to all the switches in the VTP domain. There are   three main types of advertisements:

  - Summary
 
  - Subset
 
  - Client Requests
 
**Dynamic Trunking Protocol** 

- Dynamic trunk ports are established by the switch port sending Dynamic Trunking Protocol (DTP) packets to negotiate whether the other end can be a    trunk port. If both ports can successfully negotiate an agreement, the port will become a trunk switch port. DTP advertises itself every 30 seconds   to neighbors so that they are kept aware of its status

- There are three modes:

  - Trunk
  - Dynamic Desirable (Starting Negotiate)
  - Dynamic Auto (Waiting Negotiate)

**Etherchannel Bundle**

<img width="600" height="300" alt="image" src="https://github.com/user-attachments/assets/3770f576-b545-4f29-bc08-e419c8b3fd1e" />

**Download Etherchannel Bundle with maximum 2 links Lab File (EVE)**

[Etherchannel LACP LAB File](https://github.com/Htin2001/CCNP-Enterprise/blob/7ed8cd0c7cc656a62cced2845d99423ba5ae8e95/Day%2012%20Etherchannel.zip)

- Combining multiple Physical links in to one logical link (min:2 max:8)
  
- Increases bandwidth and provides redundancy

- This has two modes:
  
  - Manual (on - on)
  
  - Dynamic (using Negotiation protocols)
  
    - LACP (IEEE 802.3ad) (Active _ Active) (Active _ Passive)
      
    - LACP advertises messages with the multicast MAC address **0180:C2000:0002**. LACP can operate in two modes :
          
      - Passive (Waiting Negotiate) 
      - Active (Do Negotiate)
              
    - PAGP (Cisco proprietary) (Desirable _ Desirable) (Desirable _ Auto)
      
    - PAgP advertises messages with the multicast MAC address **0100:0CCC:CCCC** and the protocol code **0x0104**. PAgP can operate in two modes
          
      - Auto
      - Desirable


**Troubleshooting EtherChannel Bundles**

- Port type
- Port mode
- Native VLAN
- Allowed VLAN
- Speed
- Duplex
- MTU
- Load Interval
- Storm control

**Load Balancing Traffic with EtherChannel Bundles**

- dst-ip
- dst-mac
- dst-port
- src-dst-ip
- scr-dest-ip-only
- src-dst-mac
- src-dst-mixed-ip-port
- src-dst-port
- src-ip
- src-mac
- src-mixed-ip-port
- src-port
----------------------------------------
**VTP Configuration**

`vtp version 1 or 2 or 3`

- Numbers should be same for all switches

`vtp domain (name)`

`vtp mode server / client / transparent`

`vtp password (password)`

**VTP Primary Configuration** 

`SW1# vtp primary` 

**Verifying VTP** 

`show vtp status` 

**Checking Trunklink State** 

`show interface (interface_name) switchport` 

or 

`show interface trunk` 

**Configuration Etherchannel**

`int (interface_name)` 

`channel-group (number) mode (LACP or PAGP)`

`interface port-channel (channel_group number)` 

`switchport mode trunk`

**Configure the minimum links** 

`port-channel min-links (number)` 

**Configure the max links**

`lacp max-bundle (number)` 

**LACP System Priority**

- The LACP system priority identifies which switch is the primary switch for an Etherchannel. The switch with the lowest system priority is preferred.

`lacp system-priority (number)`

`show lacp sys-id`

**LACP Interface Priority**

- Regarding with max links

- A port with a lower port priority is preferred. The small numbers will work properly.

`SW1 (config-if) # lacp port-priority 1` 

**Making LACP Fast** 

- Notes : Org Hello = 30 Sec Hold = 90 Sec 

- Fast Hello = 1 sec Hold 3 sec 

`SW1 (config-if-range) # lacp rate fast`

**Making Port as a default** 

default interface (interface_name)

**Verifying Etherchannel** 

`show etherchannel summary` 

<img width="500" height="530" alt="image" src="https://github.com/user-attachments/assets/d03c0d6f-b0cd-4cd7-8f47-ecbc92827870" />

<img width="700" height="496" alt="image" src="https://github.com/user-attachments/assets/c45a0e0a-b482-4dae-9313-be23fea36698" />

<img width="700" height="550" alt="image" src="https://github.com/user-attachments/assets/06e71dfd-229a-4e58-902b-ad4b7e2c521c" />

`show interface port-channel (port_channel number)` 

**Viewing Etherchannel Neighbor** 

` show etherchannel port` 

**Viewing LACP / PAGP Neighbor** 

`show lacp / pagp neighbor`

`show lacp / pagp neighbor detail` 

**Viewing System-id**

`show lacp sys-id` 

**Verify Etherchannel packets** 

`show lacp / pagp counters`

`clear lacp / pagp counters` 


# Day 13 (IS-IS) 

- It stands for Integrated System (Router) to Integrated System (Router)

- IGP and Link state routing protocol

- It has three tables

  - Neighbor table
  - Database table
  - Routing table 

<img width="400" height="100" alt="image" src="https://github.com/user-attachments/assets/eb29688d-1027-4f24-ac99-6bf6ba95dc8e" />

**Area and Router Roles**

- IS-IS uses different areas where the entire router sits in an area, not just one of its interfaces like with OSPF

  <img width="500" height="177" alt="image" src="https://github.com/user-attachments/assets/a1660fa2-dc1c-492d-bea8-738ff3488be1" />

- There is no backbone area, the backbone is formed by a string of routers

  <img width="500" height="499" alt="image" src="https://github.com/user-attachments/assets/34fdf69e-d188-485e-84ee-9b13ea264edc" />

- Level 1 system : this is an intra-area router, it only knows what the local area looks like and will only learn prefixes from its own area. It creates a level 1 link-state database    and SPF tree for the area
  
- Level 2 system : this is a backbone router that knows all intra-area and inter-area routes. It creates a level 2 link-state database and SPF tree for the backbone 

- Level 1-2 system : this is a router that performs both roles. It creates a separate level 1 and 2 link-state database and two SPF trees, one for each database 

- **Routers will only form neighbor adjacencies with routers that use the same level** 


# Day 14 (BFD / uRPF)

**Bidirectional Forwarding Detection (BFD)** 

<img width="500" height="346" alt="image" src="https://github.com/user-attachments/assets/02510fb3-4684-4260-ba6c-6a58e0499152" />


- BFD is a super fast protocol that is able to detect link failures within milliseconds or even microseconds
  
- There are two operating modes to BFD, **asynchronous mode** and **demand mode**
  
- The asynchronous mode is similar to the hello and holddown timers, BFD will keep sending hello packets (called BFD control packets) and when you don’t receive some of them, the        session is teared down
  
- The demand mode is different, once BFD has found a neighbor it won’t continuously send control packets but only uses a polling mechanism
  
- When a device sends BFD echo packets then the receiver will return them without processing them
  
- When the sender doesn’t get the echo packets back, it knows something is wrong and will tear down the session

<img width="800" height="224" alt="image" src="https://github.com/user-attachments/assets/b4f3d103-3e19-4faa-bd49-7c8c1c1bc363" />

<img width="634" height="575" alt="image" src="https://github.com/user-attachments/assets/b9266aa9-cb60-4c38-9f2c-f147497e2747" />

- The BFD interval is to specify how often we will send BFD packets, this is similar to the hello packet that protocols like OSPF, EIGRP, HSRP, etc

- The second value to configure is the minimum receive interval. This is how often we expect to receive a BFD packet from our neighbor

- The last value to configure is for the holddown. This is similar to the dead interval in OSPF or the holddown time that other protocols use

**Unicast Reverse Path Forwarding (uRPF)** 

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/b1a44a01-b7b0-43ae-9f22-27ddc54c5b73" />

**Download BFD & uRPF Lab File (EVE)** 

[Download BFD & uRPF Lab File](https://github.com/Htin2001/CCNP-Enterprise/blob/4081e05d1cd972bacce12675d0697d316c4e81d4/Day%2014%20BFD%20and%20uRPF.zip)

- Normally when your router receives unicast IP packets it only cares about one thing : what is the destination IP address of this IP packet so I can forward it?
  
- uRPF is a security feature that prevents these spoofing attacks. Whenever your router receives an IP packet it will check if it has a matching entry in the routing table for the       source IP address. If it doesn’t match, the packet will be discarded. uRPF has two modes:

  - **Strict mode** means that that router will perform two checks for all incoming packets on a certain interface:
 
    <img width="475" height="135" alt="image" src="https://github.com/user-attachments/assets/ba3323df-ef54-4d6a-8129-d979fd582afb" />
    
    - Do I have a matching entry for the source in the routing table?
      
    - Do I use the same interface to reach this source as where I received this packet on?
   
      <img width="700" height="800" alt="image" src="https://github.com/user-attachments/assets/c150b817-7eff-42b8-8bf4-28d7dfad6a57" />

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/ae5f2a09-50ef-4b94-b59a-382348621a21" />

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/dfb55664-f4b2-4e4a-9946-129b6e3068d5" />

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/61881fd0-d15f-4909-a163-68bf67290134" />

      - And then we create loopback on R3 for spoofing and verifying

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/ca8546a4-37db-46fb-82e5-b011b86d35ad" />

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/c9f65c67-f694-4150-b0a9-1e09016bc9f4" />

      <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/a46ff273-be1e-40ae-a2de-7e3d0f43247d" />


   - **Loose mode** means that the router will perform only a single check when it receives an IP packet on an interface
     
     - Do I have a matching entry for the source in the routing table ?

     - When it passed this check, the packet is permitted. It doesn’t matter if we use this interface to reach the source or not. Loose mode is useful when you are connected to more than one ISP and you use **asymmetric routing**.

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/d3004876-9f5d-42a3-b1e4-186497635e8c" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/d52ba870-9030-4525-89d8-99a39fb11f68" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/b4940bbf-933b-4bd6-93a5-c1b4bb930be1" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/97ad7b93-ea08-4b5f-ad79-e1e7f05d172b" />

     - And then we create loopback on R3 for spoofing and verifying

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/7cc6e8e9-ca79-4d79-aec4-99a6b35fcca7" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/f0403267-3672-4283-91d2-6b96db8962db" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/aa40c97d-b982-47be-9c0a-fec2366acc15" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/46d903e2-79cd-4c6e-b8f8-84c707681f55" />

     <img width="700" height="300" alt="image" src="https://github.com/user-attachments/assets/5ada2632-842c-4e96-83a9-d77ea8012217" />








