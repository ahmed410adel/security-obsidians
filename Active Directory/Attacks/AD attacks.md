# Reconnaissance & Enumeration
1. LDAP enumeration
2. SMB enumeration (shares, sessions)
3. NetBIOS/Net view enumeration
4. DNS/Service record (SRV) enumeration
5. RPC enumeration
6. AD object enumeration (users, groups, computers)
7. ACL discovery (object ACL scanning)
8. SPN enumeration (service principal names)
9. Kerberos ticket enumeration (klist discovery)
10. GPO and SYSVOL enumeration
11. LAPS discovery (Local Admin Password Solution objects)
12. ACL-based path discovery (BloodHound-style enumeration)
13. Domain trust enumeration (forest & external trusts)
14. AD CS / Certificate Templates discovery
15. Exchange/Outlook autodiscover enumeration
# Credential Harvesting (Local & Domain)
16. LSASS memory dumping (Mimikatz)
17. SAM hive extraction
18. NTDS.dit extraction (offline)
19. VSS shadow copy extraction of NTDS
20. Password hash dumping (hashdump)
21. Kerberos ticket theft (TGT/TGS capture)
22. Cached domain credential extraction
23. Plaintext credential discovery in files / GPP
24. Browser-stored credential harvesting via AD context
25. Password spraying against domain accounts
26. Brute-force domain accounts
27. Credential reuse exploitation across hosts
# NTLM & SMB Attacks
28. Pass-the-Hash (PtH)
29. Pass-the-Ticket (PtT)
30. Overpass-the-Hash (Pass-the-Key / Pass-the-Hash Kerberos variant)
31. NTLM relay (HTTP/SMB/LDAP)
32. SMB relay to LDAP/SMB services
33. SMB signing bypass exploitation
34. NTLM downgrade attacks
35. SMB Lateral movement (SMB exec, psexec)
36. SMB share abuse for persistence (startup scripts, scheduled tasks)
# Kerberos Attacks
37. Kerberoasting (service account TGS cracking)
38. AS-REP roasting (pre-auth disabled accounts)
39. Kerberos ticket forging — Golden Ticket (KRBTGT compromise)
40. Kerberos ticket forging — Silver Ticket (service ticket forging)
41. Ticket renewal abuse (renew TGT/TGS)
42. Abuse of RC4/weak crypto in Kerberos
43. Kerberos key extraction from memory
44. Kerberos delegation abuse (see delegation section)
# Delegation & Impersonation
45. Unconstrained delegation abuse
46. Constrained delegation abuse
47. Resource-based constrained delegation (RBCD) abuse
48. S4U2Self abuse (Service For User to Self)
49. S4U2Proxy abuse (Service For User to Proxy)
50. msDS-AllowedToActOnBehalfOfOtherIdentity (ATOBOI) abuse
51. Delegation-based lateral movement
# Replication, DC & Directory Services Attacks
52. DCSync (replication-like pull of credentials via Directory Replication)
53. DCShadow (malicious replication write)
54. AD database manipulation via replication API abuse
55. Replication permissions abuse (Replicating Directory Changes rights)
56. Rogue Domain Controller insertion
57. Metadata cleanup abuse (hiding objects)
58. AD schema abuse / schema extension injection
# AD ACL / Object/Permission Abuse
59. Abusing GenericAll / GenericWrite on objects
60. Taking ownership of objects (owner manipulation)
61. Delegation of rights via ACL misconfig
62. Service account ACL abuse (modify SPNs, change passwords)
63. Adding servicePrincipalName to controlled account
64. ACL persistence via shadow accounts
65. AdminSDHolder & protected groups abuse
66. SIDHistory abuse / injection
# GPO, SYSVOL & Group Policy Attacks
67. GPO modification for startup/logon scripts
68. GPP password retrieval (Group Policy Preferences password)
69. SYSVOL file drop (malicious startup script)
70. GPO versioning abuse for persistence
71. GPO ACL manipulation to escalate
# Service & Scheduled Task Abuse
72. Service binary replacement (service hijack)
73. Create/modify scheduled tasks for persistence
74. Abuse of service accounts for lateral movement
75. Service config ACL abuse (change binary path)
# AD Certificate Services (AD CS / PKI) Attacks
76. Certificate template abuse (enroll as privileged template)
77. Enrollment agent abuse (RPC/CA abuse)
78. SCEP/EST/auto-enrollment abuse
79. AD CS enrollment via rogue requests
80. PKI template misconfiguration exploitation
81. MS-CA impersonation / CA compromise
82. Certificate-based authentication abuse (over-privileged certs)
83. SPN certificate-based Kerberos misuse (auth certs for services)
# Azure AD / Hybrid AD / Cloud AD Attacks
84. Azure AD token theft (access token / refresh token)
85. OAuth consent phishing / malicious OAuth app consent
86. Azure AD Connect sync abuse (staging & sync account compromise)
87. Azure AD B2B collaboration abuse (guest account trust abuse)
88. Conditional Access policy bypasses (misconfig exploitation)
89. Privileged Identity Management (PIM) abuse / elevation via approval flaws
90. Azure AD Domain Services misconfig exploits
91. OAuth2.0 / OIDC flow manipulation attacks
92. Password hash sync abuse (AADC sync of NTLM hashes)
# NTLM Relay & Protocol Pivoting
93. NTLM relay to AD CS (relay to enroll cert)
94. NTLM relay to LDAP to change passwords
95. Relay to AD FS / web applications
96. SMB/HTTP/LDAP cross-protocol relay chains
# Lateral Movement & Execution
97. Pass-the-Hash / Pass-the-Ticket lateral spread (relist)
98. WMI remote execution lateral movement
99. WinRM/PSRemoting lateral execution
100. RDP credential reuse / lateral RDP hop
101. PsExec / Impacket psexec-like lateral tools
102. SMB exec via service creation
103. Scheduled task remote creation for execution
# Persistence & Backdoors
104. Creating backdoor service accounts (long-lived)
105. Golden account creation with high privileges
106. Shadow credentials / backdoor keys in AD (adcs or obj attributes)
107. Kerberos delegation backdoor via account perms
108. ACL-based stealth persistence (hidden control via ACL)
109. Malicious GPO for startup persistence
110. Credential stuffing into local SYSTEM via COM objects
# Privilege Escalation within AD
111. Exploiting misconfigured ACLs to gain Domain Admin
112. Kerberos ticket manipulations for domain admin impersonation
113. Abuse of writable domain controller object attributes
114. Abusing LDAP write rights to set msDS-ManagedBy or serviceAccountPassword
115. Local admin to domain admin escalation through lateral chains
# NTDS & Database Extraction / Backup Attacks
116. VSS snapshot theft of NTDS.dit
117. Offline NTDS extraction and cracking
118. Backup operator abuse to get AD DB files
119. ntdsutil misuse for database copy
# Detection Evasion & Cleanup
120. Golden Ticket long-lived stealth (ticket lifetime tweaks)
121. Clearing event logs remotely to hide activity
122. Time stomping & timestamp manipulation of artifacts
123. Deleting replication metadata to hide objects
124. Modifying SIEM/Windows logs via API abuse
# Protocol & Misc Advanced Attacks
125. LDAP injection for query manipulation
126. Kerberos pre-auth bypass exploitation (weak config)
127. RPC/DCOM exploitation for AD services
128. SMB protocol fuzzing for privilege escalations
129. IPv6/SMB over IPv6-specific misconfig attacks
# Defensive & Governance Abuse
130. Abuse of privileged role groups (Enterprise Admins, Schema Admins)
131. Abuse of delegation via admin delegation wizards
132. Group membership enumeration and manipulation for escalation
# Active Directory-adjacent / Physical & Social
133. LAPS shadow retrieval via backup/ACL misconfig
134. SIM swap / MFA bypass (telecom) targeting AD accounts
135. Social engineering to obtain admin creds (phishing targeting AD admins)
136. Evil maid / physical access to promote host to DC
# Tools & Graph-based Attack Facilitation (conceptual, not tools)
137. Pathfinding via BloodHound-style attack graphs (privilege path enumeration)
138. AD attack chain automation (chaining recon → exploit → escalate) 