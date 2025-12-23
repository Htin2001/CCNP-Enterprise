# CCNP-Enterprise
CCNP Labs / Theories

This includes daily updates of CCNP Enterprise's Labs and Theories.

# Day 1 (Static Route with AD Tuning)
![Day 1 UI](https://github.com/Htin2001/CCNP-Enterprise-/blob/ee511024c0821c6bcac03a9a09ce0bf263574e98/Day%201.png)
**Download Lab File (Packet Tracer File)**
[Day1 - Path Determination Lab](https://github.com/Htin2001/CCNP-Enterprise/blob/0808fb684c1ed7f3fd19f70b78272796320a52c4/Path%20Determination%20(Htin%20Aung%20Lin).pkt) 

According to this lab, we want to make a connection between PC_0 and PC_1 and we use static routes for all routers and setting AD number - 2 for the backup route which is also called AD Tuning. 

`ip route (destination network) (subnetmask) (next hop / interface name) 2-255`

**Where do we use the Administrative Distance?**
- We use AD number in choosing of best route including backup route.
- We use AD number in comparing of Routing Protocol (i.e. Three routers running both OSPF and EIGRP, we want to choose EIGRP route for these three routers, we compare AD numbers at that time. So AD number of EIGRP is 90 / 170 which AD number of OSPF is 110. Routers will choose EIGRP for routing)
 

# Day 2 (RIP Route - RIPv2 Authentication - Accesslist)

![Day 2 UI](https://github.com/Htin2001/CCNP-Enterprise/blob/c6020fc5813872bd6a2583025fe942d92ed83612/RIPv2.png)

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

<table>
  <tr>
    <td style="border:20px double black; padding:6px;">
      <img src="Sending updates every 30 seconds, next due in 4 seconds.heic" alt="Topology">
    </td>
  </tr>
</table>

- Invalid after = hold down = flushed after = 240 sec means that RIP routing status will be updated only after 240 sec even RIP protocol is down under 240 sec. But we can change by following command but **set the commend in both routers**.

`timers basic (update timer) (invalid) (holddown) (flush)` 

**RIP V2** 

- Classless routing protocol
- Supports VLSM
- Supports authentication
- Uses multicast address 224.0.0.9

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

`Key Chain (Key Chain Name - Can different)`

`Key (Number - should be same)`

`Key-string (password - should be same)`

For Interfaces between each router 

`interface (interface name)`

`ip rip authentication mode md5`

`ip rip authentication key-chain (Key Chain Name)` 


***According to our lab, we set up RIP v2 routing on all routers (R1,R2,R3) and we set authentication mode on between R2 and R3. And also we set R2's ethernet 0/0 interface as passive interface. At this point R3 stills get routing updates from R1 like (10.0.0.0/30 and 192.168.1.0/24 which can be regarded as loopback and if R1 is a external untrusted network, R3 will become a problem with huge memories. To advoid this we write access-list with distributed list in R3 router which can see in the Day 2 lab picture or set R2's ethernet0/0 interface as RIP v2 authentication.***


# Day 3 (Controlling Routing Updates | Distribute List) 

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
  - Passive - interface command is used in all routing protocols to **disable sending updates**      out from a specific interface. Since RIPv2 **doesn’t send ‘Hello packets’**, passive             interface only work on RIPv2 not for RIPv1.
- Distribution-lists
- IP - Prefix -list
- Route - maps
- Policy Based Routing


![Day 3 UI - Redistribution Loops](https://github.com/Htin2001/CCNP-Enterprise/blob/c3e0197f392ae54916823307181ac9c92c81af79/redistribution%20loops%20.png)


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

![Day 3 Filtering Routing Updates with a Distribute List](https://github.com/Htin2001/CCNP-Enterprise/blob/7814a477e2f2bd14b402f9ba0e97ed768f3bef55/Filtering%20routing%20updates%20with%20a%20distributed%20list.png)


![Day 3 Controlling Redistribution with Distribute List](https://github.com/Htin2001/CCNP-Enterprise/blob/dc83ccc43c2d7f1239dacf597eac8a0b9f84febc/Controlling%20Redistribution%20with%20Distribute%20lists%20.png)

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


