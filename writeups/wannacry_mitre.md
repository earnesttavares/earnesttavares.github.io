---
title: WannaCry & MITRE
parent: Writeups
nav_order: 1
---

<h1 style="color:#33aaff;">Threat Intelligence Report: WannaCry Ransomware</h1> 

<br>

**Threat:** WannaCry Ransomware <br>
**Date of Attack:** 2017-05-12 <br>
**Analysis Type:** Malware Intelligence Assessment <br>
**Framework:** MITRE ATT&CK <br>
**Severity:** <code style="color : red"> Critical </code> <br>
**TLP Status:** TLP: CLEAR ⚪ <br>

<br>

<h2 style="color:#D4A017;">Writeup Objective</h2>

Analyze the WannaCry ransom attack  through the lens of threat intelligence and the MITRE ATT&CK framework. Identify adversary behaviors, IoCs, and recommend defensive measures.

---

<h2 style="color:#D4A017;">Executive Summary</h2>

WannaCry, also known as WannaCrypt, was a globally significant cyberattack that occurred on May 12, 2017, attributed to the North Korean state-sponsored threat group Lazarus (APT38). The ransomware spread rapidly across vulnerable Microsoft Windows systems by exploiting the ***EternalBlue (CVE-2017-0144)*** vulnerability within the Server Message Block (SMBv1) protocol over port 445. Once executed, WannaCry encrypted the victims' files and demanded payment in Bitcoin in exchange for decryption keys. Hours later, the discovery and registration of a hardcoded kill-switch domain put an end to the global attack.

WannaCry impacted hundreds of thousands of computers worldwide and severely impacted critical organizations such as healthcare, government agencies, and private enterprises, causing massive operational disruption, financial losses, and service outages. Analysis of the malware demonstrates the use of multiple MITRE ATT&CK techniques across the Initial Access, Execution, Lateral Movement, Defense Evasion, and Impact phases.

### Immediate Triage:
* **Isolate Hosts:** Disconnect infected or unpatched machines from the network 
* **Block Port 445:** Restrict inbound and outbound SMB traffic at the firewall 
* **Deploy Signatures:** Activate IDS/IPS rules for CVE-2017-0144 detection 

### Strategic Hardening: 
* **Disable SMBv1** 
* **Apply Critical Security Patch:** MS17-010 
* **Enforce Air-Gapped Backups** 
  
---

<h2 style="color:#D4A017;">Key Findings</h2>

| Category | Assessment |
| --- | --- | 
| Malware Family | WannaCry, WannaCrypt, WanaCryptOr 2.0 | 
| Threat Type | Ransomware | 
| Initial Access | Exploitation of SMBv1 Vulnerability over port 445 | 
| Exploit Used | EternalBlue (CVE-2017-0144) | 
| Propagation Method | Self-propagating behavior | 
| Primary Objective | File Encryption & Ransom Payment | 
| Public Attribution | Lazarus Group (APT 38) | 
| Impact Level | Critical | 
| Kill Switch | Registered domain that halted spread | 

---

<h2 style="color:#D4A017;">Attack Timeline</h2>

_**➔ April 14-15 2017:**_ <br>
The Shadow Brokers released offensive hacking tools stolen from the NSA. Included in the leak was the EternalBlue exploit. This exploit targeted a vulnerability in Microsoft’s Server Message Block (SMB) protocol used for file and printer sharing. <br>

_**➔ May 12, 2017:**_ <br>
WannaCry infections began spreading globally. <br>

_**➔ Propagation Phase:**_ <br>
WannaCry used the EternalBlue exploit to propagate through networks and infect vulnerable Windows systems.  <br>

_**➔ Impact Phase:**_ <br>
Victims' files were encrypted and users were presented with ransom demands of around $300 - $600 payable in Bitcoin. <br>

_**➔ Containment:**_ <br>
Security researcher Marcus Hutchins accidentally discovered a kill switch domain within the malware’s code. Registering this domain halted the further spread of the ransomware. <br>

_**➔ Post-Incident Response:**_ <br>
Microsoft released emergency security patches (MS17-010) to address the vulnerabilities exploited by WannaCry. Organizations accelerated vulnerability remediation efforts. <br>

---

<h2 style="color:#D4A017;">Single Infection Lifecycle: From Initial Entry to File Encryption</h2>

| Step | Phase | Technical Action |
| :--- | :--- | :--- |
| **01** | **Initial Access** | Malware scans the internet/local network for exposed **TCP Port 445**. |
| **02** | **Exploitation** | Sends malicious SMBv1 packets utilizing the **EternalBlue** exploit (`CVE-2017-0144`). |
| **03** | **Deployment** | Installs the **DoublePulsar** backdoor payload directly into kernel memory. |
| **04** | **Execution** | DoublePulsar injects and executes the primary **WannaCry** ransomware payload. |
| **05** | **Persistence** | Creates a Windows service named `mssecsvc2.0` to survive system reboots. |
| **06** | **Lateral Movement** | Launches parallel threads to actively scan and infect adjacent network subnets. |
| **07** | **Impact** | Spawns the file-encryption module, targets specific file extensions, and drops the ransom note. |

---

<h2 style="color:#D4A017;">Attack Flow</h2>

```mermaid
sequenceDiagram
    autonumber
    actor WannaCry as WannaCry Malware
    participant Victim as Victim Host Space (Local)

WannaCry->>Victim: 1. Initial Access: Scans for exposed TCP Port 445
WannaCry->>Victim: 2. Exploitation: Sends EternalBlue SMBv1 Packets [T1210]
WannaCry->>Victim: 3. Deployment: Installs DoublePulsar into Kernel Memory
Note over Victim: 4. Execution: DoublePulsar Runs Ransomware Payload
Victim->>WannaCry: 5. Kill-Switch Check: Attempts to Connect to Hardcoded Domain via HTTP
Note over Victim: (If Domain Dead, Continue Execution)
Note over Victim: 6. Defense Evasion: Drops Password-Protected ZIP to Disk [T1027 / T1222.001]
Note over Victim: 7. Persistence: Creates 'mssecsvc2.0' Windows Service
Victim->>WannaCry: 8. Lateral Movement: Scans Adjacent Network Subnets [T1210]
Note over Victim: 9. Impact: Spawns Encryption Module & Ransom Note [T1489, T1490, T1486]
```

---

## [MITRE ATT&CK Matrix Mapping](https://attack.mitre.org/)

<br> 

| **Tactic** | **Technique ID** | **Technique Name** | **Behavior** | 
| :--- | --- | --- | --- |
| Initial Access | [T1190](https://attack.mitre.org/techniques/T1190/) | Exploit Public-Facing Application | WannaCry gained access through the SMBv1 vulnerability exposed on vulnerable systems through port 445. |  
| Lateral Movement | [T1210](https://attack.mitre.org/techniques/T1210/) | Exploitation of Remote Services | WannaCry contains a thread that will attempt to scan for new attached drives every 10 seconds. If one is identified, it will encrypt the files on the attached device. |       
| Defense Evasion | [T1222.001](https://attack.mitre.org/techniques/T1222/) | File & Directory Permissions Modification: Windows Permissions | Executes `attrib +h` & `icacls . /grant Everyone:F /T /C /Q` to override standard Windows access control lists by making some of its files hidden and granting all users full access controls. | 
| Impact | [T1486](https://attack.mitre.org/techniques/T1486/) | Data Encrypted for Impact | Scans local and mapped network drives, encrypts a massive list of business-critical file extensions using AES-128 and RSA-2048 encryption algorithms, leaving a `.WNCRY` file extension. | 
| Impact | [T1489](https://attack.mitre.org/techniques/T1489/) | Service Stop | WannaCry attempts to kill processes associated with Exchange, Microsoft SQL Server, and MySQL to make it possible to encrypt their data stores. | 
| Impact | [T1490](https://attack.mitre.org/techniques/T1490/) | Inhibit System Recovery | WannaCry uses `vssadmin`, `wbadmin`, `bcdedit`, and `wmic` (native system tools) to delete and disable operating system recovery features. These actions reduced the likelihood of successful recovery without paying the ransom. |  

---

<h2 style="color:#D4A017;">Threat Intelligence & MITRE ATT&CK Alignment</h2>

### Key Assessment & Evasion Tactics: 
WannaCry used strong encryption to hide its malicious activities and make it challenging to identify and analyze its payload (the key part of malware which contains the actual malicious code of the program). It used polymorphic/randomized file names for its components, making it more difficult for security tools to detect its presence. Some variants of WannaCry would self-delete to remove traces and evade detection after executing its malicious tasks. The cyberattack required minimal user interaction because the malware exploited a software vulnerability directly and bypassed typical human-targeted vectors. 

### Core Observed Behavior: 
* Network scanning 
* Automated exploitation 
* Self-propagation 
* Rapid lateral movement across internal subnets 

### Command Line Artifacts: 
* `attrib +h` 
* `icacls . /grant Everyone:F /T /C /Q`
* Analysts observed these specific commands being deployed to hide malicious files from users, override standard permission restrictions (ACLs), and actively reduce detection opportunities. 

### Persistence & Propagation Profile: 
* **Lateral Movement:** Once inside a network, it sought out other vulnerable systems within the compromised network and attempted to infect them, further expanding its reach. 
* **Autonomous Propagation:** The ransomware functioned as a network worm and autonomously sought out and infected vulnerable systems without the need for user interaction or external C2 triggers. 
* **Network Persistence:** WannaCry’s use of the SMB protocol allowed it to remain active within the network, continually searching for new targets and encrypting files on connected systems.  

---

<h2 style="color:#D4A017;">Incident Response Recommendations</h2>

If a WannaCry infection is identified: <br>

### Immediate Actions 
1. Disconnect affected hosts from the network.
2. Isolate infected systems.
3. Identify additional vulnerable devices.
4. Preserve forensic evidence.
5. Block known malicious indicators.
6. Apply Microsoft security patches (MS17-010).
7. Review backup integrity before restoration.

### Long-Term Defenses 
* Stay ahead of vulnerabilities with a fast patch management process. 
* Disable SMBv1 where possible. 
* Segment internal networks. 
* Employ endpoint detection and response (EDR).
* Conduct regular backup validation. 
* Monitor for lateral movement activity. 

---

<h2 style="color:#D4A017;">Indicators of Compromise</h2>

### Malware Binaries: 
A quick-reference database of high-fidelity SHA-256 and MD5 hashes that security tools can scan for in endpoint logs to look for the presence of the WannaCry dropper and its core components. <br> 

| Indicator Type | Cryptographic Hash Value | Artifact Description | 
| --- | --- | --- | 
| SHA-256 | ed01ebfbc9eb5bbea545af4fedebd50cba32d559101d0f7428b0d3e81e1e212b | Primary Dropper & Worm Module (`mssecsvc.exe`) | 
| SHA-256 | 84c82835a5d21bbcf75a61706d8ab549ae03c537c17153d1ec97b047854522a1 | Ransomware Encryption Orchestrator (`tasksche.exe`) | 
| MD5 | db349b97c37d22f5ea1d1841e3c89eb4 | Primary Dropper & Worm Module (`mssecsvc.exe`) | 
| MD5 | b9c5d433919d77d9228c78ad847d0784 | Ransomware Encryption Orchestrator (`tasksche.exe`) | 

### Network & Infrastructure Indicators 
* Target Ports: TCP Port 445 (SMB) 
* Kill-Switch Domain Check: Outbound HTTP requests over port 80 to: `://iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com`. 
* Outbound Signatures: Unauthorized outbound routing to Tor networks utilizing non-standard destination ports to connect with `.onion` payment verification gateways. 

### Host-Based Artifacts: 
* Created Windows Service: `mssecsvc2.0`. | Display Name: Microsoft Security Center (2.0) Service 
* Ransom File Extension: Appends `.WNCRY` to all successfully targeted files. 
* File Encryption Manager: `tasksche.exe` searches local file system for specific file extensions, generates AES keys, and begins scrambling the data into `.WNCRY` files. 
* Dropped Ransom Note: Creates text files named `@Please_Read_Me@.txt`. 

---

<h2 style="color:#D4A017;">Analyst Assessment </h2>

WannaCry remains one of the most significant ransomware incidents in history. The attack demonstrated how unpatched vulnerabilities can rapidly escalate into global operational disruptions across critical infrastructures. Analyzing WannaCry provides a practical baseline for utilizing the MITRE ATT&CK framework to map adversary behavior, optimize detection rules, and reinforce organizational resilience.



