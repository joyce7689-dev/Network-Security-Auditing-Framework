# Network Attack Surface Analysis & Defense Strategies

This module focuses on identifying common vulnerabilities within network architectures, executing controlled security assessments (Offensive), and implementing hardening strategies to mitigate identified risks (Defensive).

---

## 1. Offensive Phase: Network Reconnaissance & Exploitation

### 1.1 Enumeration & Surface Discovery
To map the internal architecture and locate potential entry points, the following methodology is applied:

* **Host Discovery & Port Scanning:**
  ```bash
  nmap -sV -sC -O -v -oA network_scan_results [Target_IP_Range]
  ```
  * *Purpose:* Identify active hosts, running services, banner versions, and the underlying operating system.
  * *Critical Flags:* `-sV` for service version detection; `-sC` for default safe scripts execution.

### 1.2 Identified Attack Vectors & Exploitation
*Below is a standard playbook for common network-level vulnerabilities simulated in a controlled environment:*

* **Vector A: Insecure Network Protocols (e.g., Telnet / FTP)**
  * **Risk:** Cleartext transmission of sensitive authentication credentials.
  * **Exploitation Technique:** Traffic sniffing using `Wireshark` / `Tshark` to capture active sessions.
  * **Proof of Concept (Methodology):** Filtering traffic by `tcp.port == 21` to isolate authentication packets and extract plain-text strings.

* **Vector B: LLMNR/NBT-NS Poisoning (Man-in-the-Middle)**
  * **Risk:** Exploiting Windows name resolution fallbacks to intercept password hashes.
  * **Exploitation Technique:** Internal network spoofing using `Responder`.
  * **Command used:** `responder -I eth0 -rdw`

---

## 2. Defensive Phase: Mitigation & Hardening Strategies

*This section details the remediation steps required to secure the infrastructure against the offensive vectors analyzed above.*

### 2.1 Protocol Deactivation & Migration
1. **Deprecate Unencrypted Services:** Disable Telnet and FTP services across all endpoints.
2. **Implement Secure Alternatives:** Migrate communication to encrypted protocols (SSH for remote administration, SFTP/HTTPS for file transfers).
3. **Disable LLMNR/NBT-NS:** Enforce Group Policy Objects (GPO) to disable LLMNR across the domain network to eliminate link-local spoofing vectors.

### 2.2 Network-Based Detection (IDS/IPS)
To detect lateral movement and active scanning, the following detection rules are proposed for an IDS like Snort:

```text
# Example Snort rule to detect aggressive Nmap SYN scans
alert tcp \(EXTERNAL_NET any ->\)HOME_NET any (msg:"POTENTIAL NMAP OS DETECTION"; flags:S; dsize:0; sid:1000001; rev:1;)
```

---

## 3. Executive Consultant Notes (Business Impact)
* **Risk Level:** High (if unencrypted administrative protocols are active).
* **Impact:** Interception of cleartext administrative credentials allows an attacker to achieve full lateral movement and domain escalation within minutes.
* **Remediation Priority:** Urgent migration to encrypted channels.
