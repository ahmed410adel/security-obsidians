# Android Pentesting – Attack List

### Application-Level Attacks

- Insecure Data Storage
- Insecure SharedPreferences Storage
- Insecure SQLite Database Storage
- Insecure External Storage Usage
- Sensitive Data Stored in Logs
- Hardcoded Secrets in APK
- Reverse Engineering of APK
- Code Obfuscation Bypass
- Tampering / Repackaging Attack
- Debuggable Application Abuse
- Insecure Cryptography
- Weak Random Number Generation
- Improper Certificate Validation
- Hardcoded Encryption Keys
- Insecure KeyStore Usage
- Improper Session Handling
- Authentication Bypass
- Authorization Bypass
- Business Logic Abuse
- Improper Input Validation
- SQL Injection in Mobile App
- Command Injection in Mobile App
- Path Traversal in Mobile App
- XSS in WebView
- WebView JavaScript Injection
- WebView File Access Abuse
- WebView Universal Access Exploit
- JavaScript Bridge Exploitation
- Intent Redirection Attack
- Activity Hijacking
- Service Hijacking
- Broadcast Receiver Hijacking
- Intent Spoofing
- Task Affinity Attack
- PendingIntent Hijacking
- Deep Link Hijacking
- Custom URL Scheme Abuse
- Fragment Injection
- Content Provider Exploitation
- Content Provider SQL Injection
- Content Provider Data Leakage
- Clipboard Data Leakage
- Tapjacking Attack
- Overlay Attack
- Accessibility Service Abuse
- Dynamic Code Loading Abuse
- Reflection Abuse
- Runtime Manipulation (Frida / Xposed)
- Root Detection Bypass
- Anti-Debugging Bypass
- Emulator Detection Bypass

---

### Network Attacks

- Man-in-the-Middle (MITM) Attack
- SSL Pinning Bypass
- Weak TLS Configuration Exploit
- Certificate Forgery Attack
- DNS Spoofing
- DNS Rebinding
- Traffic Interception
- API Endpoint Enumeration
- API Authentication Bypass
- Token Leakage in Network Traffic
- GraphQL Abuse
- WebSocket Hijacking

---

### Device / OS Attacks

- Rooted Device Exploitation
- Privilege Escalation
- Android Permission Abuse
- Exported Component Abuse
- Debug Bridge (ADB) Exploitation
- System Property Manipulation
- Binder IPC Attacks
- SELinux Policy Abuse

---

### Physical Attacks

- ADB Backup Data Extraction
- Local File System Extraction
- Memory Dump Analysis
- Cold Boot Data Recovery
- Hardware Debug Interface Exploitation

---

### Supply Chain / Distribution Attacks

- Malicious App Repackaging
- Trojanized APK Distribution
- Fake App Store Attacks
- Dependency Confusion in Mobile SDKs
- Malicious Third-Party SDK Injection

---

# iOS Pentesting – Attack List

### Application-Level Attacks

- Insecure Data Storage
- Sensitive Data in NSUserDefaults
- Keychain Data Leakage
- Insecure SQLite Storage
- Plist File Data Leakage
- Hardcoded Secrets in IPA
- Binary Reverse Engineering
- Weak Cryptography Usage
- Hardcoded Encryption Keys
- Improper Random Number Generation
- Improper Certificate Validation
- Improper Session Management
- Authentication Bypass
- Authorization Bypass
- Business Logic Abuse
- Improper Input Validation
- SQL Injection in Mobile App
- Command Injection in Mobile App
- Path Traversal in Mobile App
- WebView XSS
- JavaScript Injection in WKWebView
- WebView Local File Access Abuse
- Custom URL Scheme Hijacking
- Deep Link Abuse
- Universal Link Hijacking
- Pasteboard Data Leakage
- Clipboard Leakage
- Screenshot Data Leakage
- Debugging Enabled in Production
- Runtime Method Swizzling Abuse
- Dynamic Library Injection
- Jailbreak Detection Bypass
- Anti-Debugging Bypass
- Frida Runtime Manipulation
- Objective-C Runtime Exploitation

---

### Network Attacks

- Man-in-the-Middle Attack
- SSL Pinning Bypass
- ATS (App Transport Security) Bypass
- Weak TLS Configuration Exploit
- Traffic Interception
- API Endpoint Enumeration
- API Authentication Bypass
- Token Leakage in Traffic
- GraphQL Abuse
- WebSocket Hijacking

---

### Device / OS Attacks

- Jailbreak Exploitation
- Privilege Escalation
- Sandbox Escape
- Entitlement Abuse
- IPC Service Abuse
- Mach Port Exploitation
- iOS Permission Abuse

---

### Physical Attacks

- Backup Extraction Attack
- iTunes Backup Decryption Attack
- File System Extraction
- Memory Dump Analysis
- Hardware Debug Interface Exploitation

---

### Supply Chain Attacks

- Malicious Enterprise Certificate Distribution
- Trojanized IPA Distribution
- SDK Injection Attacks
- Malicious CocoaPods Dependency