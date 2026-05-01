When a device joins a network, it usually doesn’t know:
- Its IP address
- The subnet mask
- The default gateway
- The DNS server

So it broadcasts:
*“Is there a DHCP server here?”*

This is the **DORA process**:
1. **Discover** – Client broadcasts request.
2. **Offer** – DHCP server offers configuration.
3. **Request** – Client asks to use that offer.
4. **Acknowledge** – Server confirms.
Along with the IP address, the server sends **DHCP options**.

#### What are DHCP options?
| Option | Meaning         |
| ------ | --------------- |
| 1      | Subnet mask     |
| 3      | Default gateway |
| 6      | DNS server      |
| 15     | Domain name     |
| 51     | Lease time      |

## What is DHCP spoofing?
DHCP spoofing = attacker sets up a **rogue DHCP server** inside the network.
When a victim device connects:
- The attacker replies faster than the legitimate DHCP server.
- The victim accepts the attacker’s configuration.
- The attacker controls networking parameters.

## Protections

1. DHCP Snooping
	What DHCP Snooping does
	Switch marks ports as:
	- **Trusted** → where the real DHCP server exists
	- **Untrusted** → user access ports