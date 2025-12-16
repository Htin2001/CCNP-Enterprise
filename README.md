# CCNP-Enterprise
CCNP Labs / Theories

This includes daily updates of CCNP Enterprise's Labs and Theories.

# Day 1 (Static Route with AD Tuning)
![Day 1 UI](https://github.com/Htin2001/CCNP-Enterprise-/blob/ee511024c0821c6bcac03a9a09ce0bf263574e98/Day%201.png)
Download file: (https://github.com/Htin2001/CCNP-Enterprise/blob/b4982b4bb50f1f06aee14f70cde97288cf18304d/Path%20Determination%20(Htin%20Aung%20Lin).pkt)

According to this lab, we want to make a connection between PC_0 and PC_1 and we use static routes for all routers and setting AD number - 2 for the backup route which is also called AD Tuning. 

`ip route (destination network) (subnetmask) (next hop / interface name) 2-255`

**Where do we use the Administrative Distance?**
- We use AD number in choosing of best route including backup route.
- We use AD number in comparing of Routing Protocol (i.e. Three routers running both OSPF and EIGRP, we want to choose EIGRP route for these three routers, we compare AD numbers at that time. So AD number of EIGRP is 90 / 170 which AD number of OSPF is 110. Routers will choose EIGRP for routing)
 

# Day 2 (RIP Route - RIPv2 Authentication - Accesslist)


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
- Periodic updates and Exchange entire routing for every 30 seconds but we can change them. The commend -

`show ip protocol (Checking which routing protocol is running on routers)`

![Day2 UI](https://github.com/Htin2001/CCNP-Enterprise/blob/388f43b6e191c5ebed437ace9c90758b16f1b286/Sending%20updates%20every%2030%20seconds%2C%20next%20due%20in%204%20seconds.heic)
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


**If we do not want to provide routing updates for unnecessery interface in router**


`router rip`

`passive-interface (interface name)`

 
 
