*When building networks we need to consider two things*
# 1. Media
#### Guided
- UTP
	**when connecting two devices using UTP cable**
	if two devices are the same will connect them (crossover) except router and pc
	if two devices are NOT the same will connect them (straight)
	![[Untitled.png]]
	*if the devices allow MIDIX feature it does not matter the order of code*
- Fiber optics
- Coaxial cable
#### Unguided
- Radio wave (WIFI)

---

# 2. Network Device
## 1. Switch
### 1.1 Hierarchy Switch Design
1. Collapsed core (2 tier)
hierarchy without core or distribution
**why we use distribution switch ?**
*to save money of buying fiber cable for every access switch to connect it to core switch*
![[Pasted image 20251101184350.png]]
2. full hierarchy (3 tier)
![[Screenshot 2025-11-01 184401.png]]
3. simplified campus (VSS - stack wise)
- `Stackwise`

![[Pasted image 20251101184842.png]]

- `VSS`
![[Untitled-1.png]]
4. spine and leaf
![[Pasted image 20251101185158.png]]
5. SD-Access (DNA)
---
### 1.2 Switch Technology
- switch do broadcast firstly to store the MAC addresses of the connected devices to him in MAC address table 
	`ARP` is the protocol that do this task
	*switch do broadcast when he asked for MAC address that is not stored on his MAC address table*
- switch use `CSMA/CD` protocol to avoid collision on network in half duplex
- `STP` spanning tree protocol `802.1d` this protocol has purposes :
	- root election
		**but why does we need to have a root switch ?**
		to decrease the number of broadcasts in the domain
	- sending `BPDU` (bridge protocol data unit) message contain :
		- priority (start with 32000) increase in this pattern (0 - 4096)
		- mac address
	- sending `TCN` (topology change notification) 
		if the route to the root change
	- sending `TCA` (topology change acknowledgment) 
		everything is stable now
	- loop prevention (redundance) --> standby
	- `rapid` protocol (this protocol for switches)
		if the normal path closed redundant path will run by sending BPDU by every switch not only the root
	- `portfast` protocol (this protocol for PCs)
		this protocol make the PC connect immediately and don't wait 30 seconds to connect
- switch port mode
	- listening 15 sec
	- learning 15 sec
	- forward
- VLAN
	**what is the role of VLAN ?**
	increase broadcast domain and decrease broadcast size
- `DTP dynamic trunking protocol` allow switch to negotiate with other switches to decide port mode
- `VTP vlan trunking protocol`
	VTP mode :
	- transparent -> do not copy any VLANs to other and keep the revision equal zero 
	- server/client -> allow server to copy VLANs to other servers and client and has the highest revision *must add password first to allow client/server mode*
- `root guard` is a feature that secure switch from manipulation by receiving message that he is not the root
- `BPDU guard`
- `BPDU filter`
- `STP loop gaurd` protect when using fiber cable that has a broken sender/receiver port
- `unidirectional link detection` modes :
	aggressive : close port (Err-detection)
	normal : alerting
- `storm control`
- `port security` stick MAC address of devices to their connected port and take one of 3 action the MAC address of the port changed (shutdown - protect - restrict) 
- `EtherChanel/aggregation port` a way of mixing port using protocols and allow load sharing like `PAGP` or `LACP` 

Port modes
![[DTP_modes.png]]

###### Notes :
- we could use layer 2 or 3 switches on every hierarchy
- normal switch (connected to PCs) = designated
- BPDU only sent by root
- if the device did not act in 5 minutes it mac address will deleted from mac address table
- `P2P` means full duplex
- switch fabric means every switch on the network
- `vlan.dat` store VLAN configuration
- you should apply `dot1q` (name tagging protocol) that allow VLANs to be understanded by switches
- VLAN 1 is untagged 
- you should apply `dot1q` (name tagging protocol) that allow VLANs to be understanded by switches
- SVI switch virtual interface gives an IP to the switch
- best practice is to close the native VLAN
- PVST -> lead balancing to make switch root for every VLAN
- `pruning` is a feature that drop data if it comes to a switch that does not contain the VLAN of the data 
- VLANs must not be on the same subnet
## 2. Router
**routers has 3 modes**
- user mode
- privilege mode
- EXIC mode
*if the router shows dialog mode this mean that the router is new and did not store any thing*
### Router technology 1.1
- MPLS or VXLAN protocols allow VLANs to have IPs and work in layer 3
- static route
- dynamic route `RIP`, `OSPF`
	RIP problems : every 30 seconds will alter the table even if the table do not need any update and have max hope = 16 and RIP is classful (do not understand subnet mask)





###### Notes :
- *we connect router and switch first time using console port* which use cable have side console and side serial (DP9) 
	if you do not have this cable create it using normal UTP cable but connected in rollover way and use converter LAN to serial
	![[Pasted image 20251101175453.png]]
	**why don't use the LAN port to connect to the router or switch ?**
	because the device did not take IP yet.

---
##### Protocols & Numbers
- `http` 80
- `https` 443
- `ftp` 21
- `smtp` 25
- `pop` 110
- `telnet` 23
- `rdp` 3389

##### Messages type
- unicast
- multicast
- broadcast

## Subnet Mask Classes

| mask          | class |
| ------------- | ----- |
| 255.255.255.0 | C     |
| 255.255.0.0   | B     |
| 255.0.0.0     | A     |
###### why do not use class A always ?
because the broadcast process will abuse the mask and send message to every possible device in the mask which will lead to broadcast storm

## Custom Subnet Mask
- Equation of creating mask hold number of devices you want `2^n - 2 = number of devices in the network` n is the number of ZEROs from right and the rest is ONEs this is the mask 
- mask `AND` IP = subnet
- broadcast IP = same IP but change the last (n) bits into ONEs
- block size = last one on the mask 
- number of subnets in network = `2^(number of ones in the used octet)`
###### how to now that  2 IPs in the same subnet ?
mask `AND` IP for the two IPs and get the subnet of them

*192.168.0.1/22* = CIDR (classless interdomain routing)

---
**address classes**
![[Pasted image 20251101171241.png]]

---
## TCP vs UDP
**Start (3 way handshake)**
![[Screenshot 2025-11-01 182535.png]]

**Finish** 
![[Screenshot 2025-11-01 182542.png]]

---










# Network Scenario
1. know the number of devices on the network
2. buy routers and switches
3. connect to router using CONSOL port
4. configure switch and router
5. connect end devices into switch


[[Network Attack List]]