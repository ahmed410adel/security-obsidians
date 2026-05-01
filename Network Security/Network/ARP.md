**ARP (Address Resolution Protocol)** connects IP addresses to MAC addresses on an IPv4 LAN.




###### NOTES :
- ARP is stateless and trustful: most stacks accept ARP replies even if they didn’t ask for them.
- A reply can update the ARP table (this is how poisoning works).
- 