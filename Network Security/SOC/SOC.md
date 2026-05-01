## Network Artifacts

## SOC is all about detection and response
### 1. Detection
- **Detect vulnerabilities**: A vulnerability is a weakness that an attacker can exploit to carry out things beyond their permission level. Strictly speaking, vulnerabilities are not necessarily the SOC’s responsibility; however, unfixed vulnerabilities affect the security level of the entire company.
- **Detect unauthorized activity**: Consider the case where an attacker discovered the username and password of one of the employees and used them to log in to the company system. It is crucial to detect this kind of unauthorized activity quickly before it causes any damage. Many clues, such as geographic location, can help us detect this.
- **Detect policy violations**: A security policy is a set of rules and procedures created to help protect a company against security threats and ensure compliance. What is considered a violation would vary from company to company; examples include downloading pirated media files and sending confidential company files insecurely.
- **Detect intrusions**: Intrusions refer to unauthorized access to systems and networks. One scenario would be an attacker successfully exploiting our web application. Another would be a user visiting a malicious site and getting their computer infected.
### 2. Response
- **Support with the incident response**: Once an incident is detected, certain steps are taken to respond to it. This response includes minimizing its impact and performing the root cause analysis of the incident. The SOC team also helps the incident response team carry out these steps.
##### SOC Team
![[6645aa8c024f7893371eb7ac-1718872774537.png]]
- **SOC Analyst (Level 1):** Anything detected by the security solution would pass through these analysts first. These are the first responders to any detection. SOC Level 1 Analysts perform basic alert triage to determine if a specific detection is harmful. They also report these detections through proper channels.
- **SOC Analyst (Level 2)**: While Level 1 does the first-level analysis, some detections may require deeper investigation. Level 2 Analysts help them dive deeper into the investigations and correlate the data from multiple data sources to perform a proper analysis.
- **SOC Analyst (Level 3)**: Level 3 Analysts are experienced professionals who proactively look for any threat indicators and support in the incident response activities. The critical severity detection reported by Level 1 and Level 2 Analysts are often security incidents that need detailed responses, including containment, eradication, and recovery. This is where Level 3 analysts’ experience comes in handy.
- **Security Engineer:** All analysts work on security solutions. These solutions need deployment and configuration. Security Engineers deploy and configure these security solutions to ensure their smooth operation.
- **Detection Engineer:** Security rules are the logic built behind security solutions to detect harmful activities. Level 2 and 3 Analysts often create these rules, while the SOC team can sometimes also utilize the detection engineer role independently for this responsibility.
- **SOC Manager:** The SOC Manager manages the processes the SOC team follows and provides support. The SOC Manager also remains in contact with the organization’s CISO (Chief Information Security Officer) to provide him with updates on the SOC team’s current security posture and efforts.
#### some questions that need to be answered during the triage of an alert.

| What?  | A malicious file was detected on one of the hosts inside the organization’s network.                                                                                                                                          |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| When?  | The file was detected at 13:20 on June 5, 2024.                                                                                                                                                                               |
| Where? | The file was detected in the directory of the host: "GEORGE PC".                                                                                                                                                              |
| Who?   | The file was detected for the user George.                                                                                                                                                                                    |
| Why?   | After the investigation, it was found that the file was downloaded from a pirated software-selling website. The investigation with the user revealed that they downloaded the file as they wanted to use a software for free. |

## when to escalate the alert to L2 ?
1. The alert is an indicator of a major cyberattack requiring deeper investigation or DFIR
2. Remediation actions like malware removal, host isolation, or password reset are required
3. Communication with customers, partners, management, or law enforcement agencies is required
4. You just do not fully understand the alert and need some help from more senior analysts
![[678ecc92c80aa206339f0f23-1743520297119.svg]]
#### Technology used :
1. SEIM
2. EDR (Endpoint Detection and Response)
3. Firewall
4. IPS/IDS
5. EPP
6. XDR
7. SOAR

---
1. new user-agent on your network
2. C2 information
3. URI patterns followed by the HTTP POST requests
	![[a6e36c7601f7b4ec07ce2a102ffb33ab.png]]


[[SOC Related Attacks]]
[[tools]]

---

### Information
- SOC team uses  **SIEM** (Security information and event management) and **EDR** (Endpoint Detection and Response) tools to monitor suspicious and malicious network activities.
- **Host artifacts** are the traces or observables that attackers leave on the system, such as registry values, suspicious process execution, attack patterns or IOCs (Indicators of Compromise), files dropped by malicious applications, or anything exclusive to the current threat.
- Fuzzy hashing is also a strong weapon against the attacker's tools. Fuzzy hashing helps you to perform similarity analysis
