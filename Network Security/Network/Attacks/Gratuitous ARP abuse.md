**Gratuitous ARP** is an ARP _announcement_ where a host broadcasts an ARP reply (or ARP request) that maps _its_ own IP to _its_ MAC — without being asked. Remember that **GARP is a normal, legitimate network function**. It becomes an _attack_ only when someone sends forged gratuitous ARP announcements to mislead others about who owns an IP
###### Legitimate use :
- IP conflict detection
- VM migration
- failover (VIP announcement)
- quickly updating switches/hosts after a MAC move

## Idea behind attack
Gratuitous ARP attacks start with a simple premise – a device sends out an ARP message announcing that it has taken over the IP address of another device on the network, even if it hasn’t. This false announcement disrupts the regular operation of ARP, leading to confusion among devices trying to communicate.
### Steps to produce the attack
1. Attacker identifies a target IP to hijack (common targets: gateway IP, DNS server, VIP, database server) #Preparation/Recon
2. Attacker picks/sets a MAC address on their interface (often spoofs the MAC to look like legitimate device or a new MAC). #Preparation/Recon
3. Attacker confirms they are on the same broadcast domain #Preparation/Recon
4. Attacker sends **one or more gratuitous ARP replies** (ARP op=2) that say: (without ARP request) #Initiate/Attack
	`sender IP = victim_IP`
	`sender MAC = attacker_MAC`
	`target IP = victim_IP`
	**Effect after initial claim:** Clients begin sending Ethernet frames destined to `victim_IP` to the attacker MAC. If the attacker forwards those frames, they become a MitM; if the attacker drops them, it’s a DoS.
5. Attacker commonly **repeats GARPs periodically** (or floods) to keep overwriting correct ARP entries. #Maintain/Persistence
6. Once traffic arrives at attacker MAC, attacker can do a lot of things #Exploitation/Attacking
	- Forward transparently (MitM / sniff / modify).
	- Drop packets (blackhole / DoS).
	- Inject responses (e.g., spoof DNS answers).

## How to tell whether a GARP you see is benign or malicious
- **Benign indicators**:
    - Occurs during VM migration, cluster failover, or expected maintenance window.
    - Source MAC is known/whitelisted (or matches orchestrator MACs).
    - Single or short burst of GARP coincident with an administrative event.
- **Malicious indicators**:
    - Repeated GARPs for the same critical IP mapping to a new/unknown MAC across many hosts.
    - Occurs outside maintenance windows with no preceding legitimate failover/migration.
    - Coincides with traffic sniffing or service anomalies.
    - Switch CAM shows the suspect MAC on unexpected access port.

## Impact
1. Network Disruption and Downtime
2. - **Man-in-the-Middle (MitM)**: intercept traffic between hosts (often when attacker also forwards traffic to avoid disruption).
3. **DoS / Blackhole**: announce victim IP mapping to a nonexistent MAC — traffic is dropped.
4. **IP takeover**: claim an IP (e.g., cluster IP, gateway IP) and receive that traffic.
5. **Bypassing filtering**: if some controls trust MAC addresses.


*These attacks can act as a smokescreen*
## Tools used for attack


## Attack checklist


## Mitigations


###### NOTES :
- if attacker is not L2-adjacent, GARPs will never reach victim hosts.