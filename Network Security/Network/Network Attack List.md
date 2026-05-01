## Layer 2 / LAN / Switching / ARP / DHCP
1. [[Gratuitous ARP abuse]]
2. ARP cache poisoning (duplicate entry variants)
3. ARP table exhaustion
4. MAC flooding (CAM exhaustion)
5. VLAN double tagging (802.1Q double-tagging)
6. Switch spoofing / rogue trunking
7. DHCP starvation (exhaustion)
8. Rogue DHCP server
9. DHCP ACK/NAK attacks
10. [[DHCP spoofing]]
## LAN sniffing / MITM / proxying
11. Passive packet sniffing on switched networks (with port mirror)
12. Active MITM via ICMP redirects
13. Man-on-the-side attacks
14. SSL/TLS interception with rogue CA (TLS MitM)
15. SSL stripping (downgrade) — already in your list but different variants exist
16. Transparent proxy hijack
17. Web proxy chaining / proxy auto-config poisoning (PAC file attacks)
## IP / routing / backbone attacks
18. BGP route leak
19. BGP prefix hijack (you listed) — subprefix hijack
20. Route injection via compromised router
21. AS path manipulation
22. IP fragmentation attacks (overlapping fragments / teardrop)
23. ICMP redirect exploitation (routing manipulation)
24. Routing table poisoning (static/bgp variants)
## Spoofing / identity
25. IP address spoofing (you listed) — variants for TCP/UDP/ICMP
26. MAC address spoofing (you listed)
27. [[DNS-based identity spoofing (fake records or hosts file)]]
28. NTP spoofing (time skew + auth bypass attempts)
## DoS / DDoS / flooding (protocol-level)
29. UDP flood
30. TCP RST flood
31. TCP ACK flood
32. TCP FIN flood
33. SYN-ACK flood
34. Slowloris (HTTP slow request DoS)
35. Slow POST / Slowread (application-layer slow DoS)
36. HTTP/2 flood attacks
37. SSL/TLS handshake flood
38. Fragmentation-based flood (random fragment flood)
39. ACK/PSH flood
40. TCP window-size manipulation DoS
41. Application-layer DDoS (HTTP GET/POST floods)
## Amplification & reflection (more variants)
42. [[DNS amplification]]
43. NTP monlist reflection (listed)
44. Chargen reflection
45. SSDP/UPnP amplification
46. LDAP amplification
47. SNMP amplification
48. CLDAP amplification
49. Memcached amplification (listed)
50. CHARGEN/echo:protocol abuses
## ICMP / ping-based
51. ICMP flood (ping flood)
52. Smurf (listed) — Fraggle (UDP variant)
53. ICMP unreachable exploitation
## Wireless-specific (more variants)
54. Deauthentication attack (Wi-Fi deauth)
55. Disassociation attack
56. Beacon frame spoofing / SSID spoofing
57. Probe-request harvesting / client tracking
58. KRACK (key reinstallation attack)
59. PMKID capture (WPA2/PMKID attack)
60. Wi-Fi Protected Setup (WPS) PIN brute force / Pixie Dust
61. Wireless frame injection / packet replay
62. Bluetooth pairing attacks / bluejacking / bluesnarfing
## DNS / name service attacks
63. [[DNS rebinding]]
64. [[DNS tunneling]]
65. [[DNS amplification NXDOMAIN flood]]
66. [[DNSSEC downgrade]]
67. [[DNS cache snooping]]
## Tunneling / covert channels / evasion
68. IP-in-IP tunneling (encapsulation abuse)
69. [[DNS over HTTPS or TLS abuse for tunneling (DoH or DoT tunneling)]]
70. HTTP tunneling / WebSocket tunneling for C2
71. SSH tunneling abuse (pivoting)
72. GRE tunneling misuse
73. ICMP tunneling (covert channel)
74. UDP encapsulation covert channels
75. [[DNS TXT or GPT-based covert channels]]
## Application / protocol misuse over the network
76. SMTP open relay abuse / email spoofing
77. SMB relay attacks
78. NetBIOS name service poisoning
79. LDAP injection (network observable)
80. RDP brute force / BlueKeep-style exploitation (network vector)
81. SNMP write/SET misuse (configuration manipulation)
82. SIP enumeration / SIP INVITE flood (VoIP attacks)
83. RTP/VoIP media injection / call hijacking
## Authentication / session related (network vectors)
84. TCP session fixation / session replay (network-based)
85. Kerberos pass-the-ticket / relay (network-enabled variants)
86. NTLM relay / NTLM spoofing over SMB/HTTP
87. OAuth token interception via network flows (bearer tokens leakage)
## Middlebox / service evasion and exploitation
88. Firewall/NAT traversal techniques (UDP hole punching abuse)
89. NAT pinning / sticky NAT abuse
90. Application-layer protocol confusion attacks (smuggling variants)
91. HTTP request smuggling (application/network boundary)
92. HTTP response splitting / header injection (network-visible)
## Monitoring / measurement & poisoning
93. Netflow / sFlow data manipulation (supply false telemetry)
94. RADIUS/TACACS+ spoofing / authentication bypass
95. NTP amplification + time-based attacks (listed variant)
96. Management plane attacks (SNMP, SSH to network devices)
97. Telnet/FTP cleartext credential capture (legacy protocol abuse)
## Advanced persistent / red-team style network attacks
98. C2 over HTTP(S)/DNS/ICMP (command-and-control channels)
99. Lateral movement using SMB/PSExec/WMI over network
100. Pass-the-hash / credential replay across the network
101. ARP + DHCP combined advanced persistence (malicious gateway)
102. Pivoting through compromised routers or firewalls
## Emerging / niche / infrastructure
103. IoT protocol attacks (MQTT, CoAP abuse)
104. UPnP WAN-PMP abuses (exposing internal services)
105. Cloud network misconfiguration exploitation (VPC peering abuse)
106. SDN controller compromise / flow rule poisoning (software-defined networking)
107. Kubernetes network policy bypass / CNI plugin abuse (cluster networking attacks)
108. IPv6-specific attacks (RA spoofing, ND spoofing, SLAAC manipulation)
109. IPv6 extension header abuse (packet drop/evasion)
110. Segment routing misconfiguration exploits (SRv6)
111. QUIC protocol-specific attacks (connection/state exhaustion)
## Side-channel / quality-of-service & performance attacks
112. QoS/DSCP manipulation to degrade service for select traffic
113. Packet reordering / delay injection (latency attacks)
114. TCP RST injection to tear down connections
115. Path MTU manipulation (fragmentation behavior abuse)
## Social / physical network attacks (network-adjacent)
116. Cable tapping / physical wire interception
117. Evil maid / unattended device network compromise (plant rogue AP)
118. SIM swap / mobile network UUID/IMSI attacks (telecom network attacks)
119. SS7 signaling attacks (telecom-level interception/spoofing)