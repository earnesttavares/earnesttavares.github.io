---
title: WannaCry & MITRE
parent: Writeups
nav_order: 1
---

# Threat Intelligence Report: WannaCry Ransomware 

<br>

**Threat:** WannaCry Ransomware <br>
**Date of Attack:** 2017-05-12 <br>
**Analysis Type:** Malware Intelligence Assessment <br>
**Framework:** MITRE ATT&CK <br>
**Severity:** Critical <br>
**TLP Status:** TLP: CLEAR ⚪ <br>
**Targeting Strategy:** Opportunistic / Indiscriminate Global Scanning <br> 
**Highly Impacted Sectors:** Healthcare, Telecommunications, Logistics <br>

<br>

## Executive Summary 

WannaCry, also known as WannaCrypt, was a globally significant cyberattack that occurred on May 12, 2017, attributed to the North Korean state-sponsored threat group Lazarus (APT38). The ransomware spread rapidly across vulnerable Microsoft Windows systems by exploiting the ***EternalBlue (CVE-2017-0144)*** vulnerability within the Server Message Block (SMBv1) protocol over port 445. Once executed, WannaCry encrypted the victims' files and demanded payment in Bitcoin in exchange for decryption keys. Propagation was heavily mitigated hours later by the discovery of an internal hardcoded kill-switch domain.

The attack impacted hundreds of thousands of computers worldwide and affected critical organizations such as healthcare, government agencies, and private enterprises, causing massive operational disruption, financial losses, and service outages. Analysis of the malware demonstrates the use of multiple MITRE ATT&CK techniques across the Initial Access, Execution, Lateral Movement, Defense Evasion, Persistence, and Impact phases.

### Immediate Triage:
* **Isolate Hosts:** Disconnect infected or unpatched machines from the network
* **Block Port 445:** Restrict inbound and outbound SMB traffic at the firewall
* **Deploy Signatures:** Activate IDS/IPS rules for CVE-2017-0144 detection 

### Strategic Hardening: 
* **Disable SMBv1**
* **Apply MS17-010:** Critical security patch
* **Enforce Air-Gapped Backups**
  
---

## Key Findings 

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

## Attack Timeline 

**April 14-15 2017:** <br>
The Shadow Brokers released offensive hacking tools stolen from the NSA. Included in the leak was the EternalBlue exploit. This exploit targeted a vulnerability in Microsoft’s Server Message Block (SMB) protocol used for file & printer sharing. <br>

**May 12, 2017:** <br>
WannaCry infections began spreading globally. <br>

**Propagation Phase:** <br>
WannaCry used the EternalBlue exploit to propagate through networks & infect vulnerable Windows systems.  <br>

**Impact Phase:** <br>
Victims' files were encrypted and users were presented with ransom demands of around $300 - $600 payable in Bitcoin. <br>

**Containment:** <br>
Security researcher Marcus Hutchins accidentally discovered a kill switch domain within the malware’s code. Registering this domain halted the further spread of the ransomware. <br>

**Post-Incident Response:** <br>
Microsoft released emergency security patches (MS17-010) to address the vulnerabilities exploited by WannaCry. Organizations accelerated vulnerability remediation efforts. <br>

---

## Single Infection Lifecycle: From Initial Entry to File Encryption 

**Initial Access** >> Malware scans internet/local network for exposed TCP Port 445. <br>

**Exploitation** >> Sends malicious SMBv1 packets utilizing the EternalBlue exploit (CVE-2017-0144). <br>

**Implantation** >> Installs the DoublePulsar backdoor payload into kernel memory. <br>

**Execution** >> DoublePulsar injects and executes the primary WannaCry ransomware payload. <br>

**Kill-Switch Check** >> Malware attempts to connect to the hardcoded domain via HTTP. <br>

**Defense Evasion** >> Drops a password-protected zip file containing component binaries to disk. <br>

**Persistence** >> Creates a Windows service named `mssecsvc2.0` to ensure persistence across reboots. <br>

**Lateral Movement** >> Launches parallel threads to actively scan and infect adjacent network subnets. <br>

**Impact** >> Spawns the file-encryption module, targets specific file extensions, and drops the ransom note. <br>

## Attack Flow 

```mermaid
flowchart LR
    A[Unpatched Windows Host] -->
B[EternalBlue Exploit<br/>CVE-2017-0144]
    B --> C[Initial Compromise]
    C --> D[SMB Network Scanning]
    D --> E[Lateral Movement]
    E --> F[Remote System Infection]
    F --> G[File Encryption]
    G --> H[Ransom Demand]
    E --> D
```

---

## MITRE ATT&CK Matrix Mapping

<br> 

| **Tactic** | **Technique ID** | **Technique Name** | **Behavior** | 
| :--- | --- | --- | --- |
| Initial Access | T1190 | Exploit Public-Facing Application | WannaCry gained access through the SMBv1 vulnerability exposed on vulnerable systems through port 445. |  
| Lateral Movement | T1210 | Exploitation of Remote Services | WannaCry contains a thread that will attempt to scan for new attached drives every 10 seconds. If one is identified, it will encrypt the files on the attached device. |       
| Defense Evasion | T1222.001 | File & Directory Permissions Modification: Windows Permissions | Executes `attrib +h` & `icacls . /grant Everyone:F /T /C /Q` to override standard Windows access control lists by making some of its files hidden and granting all users full access controls. | 
| Impact | T1486 | Data Encrypted for Impact | Scans local and mapped network drives, encrypts a massive list of business-critical file extensions using AES-128 and RSA-2048 encryption algorithms, leaving a `.WNCRY` file extension. | 
| Impact | T1489 | Service Stop | WannaCry attempts to kill processes associated with Exchange, Microsoft SQL Server, and MySQL to make it possible to encrypt their data stores. | 
| Impact | T1490 | Inhibit System Recovery | WannaCry uses `vssadmin`, `wbadmin`, `bcdedit`, and `wmic` (native system tools) to delete and disable operating system recovery features. These actions reduced the likelihood of successful recovery without paying the ransom. |  

<br>

### Key Assessment: 
WannaCry used strong encryption to hide its malicious activities and make it challenging to identify and analyze its payload (the key part of malware which contains the actual malicious code of the program). It used randomized file names for its components, making it more difficult for security tools to detect its presence. Some variants of WannaCry would self-delete to remove traces and evade detection after executing its malicious tasks. The cyberattack required minimal user interaction because the malware exploited a software vulnerability directly and bypassed typical human-targeted vectors. 

### Core Observed Behavior: 
* Network scanning
* Automated exploitation
* Self-propagation
* Rapid internal spread

### Command Line Artifacts: 
* `attrib +h`
* `icacls . /grant Everyone:F /T /C /Q`
* Analysts observed these specific commands being deployed to hide malicious files from users, override standard permission restrictions, and actively reduce detection opportunities.  

### Persistence Profile: 
* **Lateral Movement:** Once inside a network, it sought out other vulnerable systems within the compromised network and attempted to infect them, further expanding its reach.
* **Self-Propagation:** The ransomware autonomously sought out and infected vulnerable systems without the need for user interaction.
* **Network Persistence:** WannaCry’s use of the SMB protocol allowed it to remain active within the network, continually searching for new targets and encrypting files on connected systems. 

---

## Incident Response Recommendations
If a WannaCry infection is identified: <br>

### Immediate Actions 
1. Disconnect affected hosts from the network.
2. Isolate infected systems.
3. Identify additional vulnerable devices.
4. Preserve forensic evidence.
5. Block known malicious indicators.
6. Apply Microsoft security patches (MS17-010)
7. Review backup integrity before restoration.

### Long-Term Defenses 
* Stay ahead of vulnerabilities with a fast patch management process.
* Disable SMBv1 where possible.
* Segment internal networks.
* Employ endpoint detection and response (EDR).
* Conduct regular backup validation.
* Monitor for lateral movement activity.

---

## Indicators of Compromise:

### Malware Binaries 
Quick-reference database of high-fidelity SHA-256 and MD5 hashes that security tools can scan for in endpoint logs to look for the presence of the WannaCry dropper and its core components. <br> 

| Indicator Type | Cryptographic Hash Value | Artifact Description | 
| --- | --- | --- | 
| SHA-256 | ed01ebfbc9eb5bbea545af4fedebd50cba32d559101d0f7428b0d3e81e1e212b | Primary WannaCry worm executable with dropper. | 
| SHA-256 | 84c82835a5d21bbcf75a61706d8ab549ae03c537c17153d1ec97b047854522a1 | Unpacked secondary ransomware encryption module. | 
| MD5 | db349b97c37d22f5ea1d1841e3c89eb4 | Standard initial dropper payload signature. | 
| MD5 | b9c5d433919d77d9228c78ad847d0784 | The `tasksche.exe` component responsible for orchestrating file encryption. | 

### Network & Infrastructure Indicators 
* Target Ports: TCP Port 445 (SMB)
* Kill-Switch Domain Check: Outbound HTTP requests over port 80 to: `://iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com`.
* C2 Infrastructure: Unauthorized outbound routing to Tor networks utilizing non-standard destination ports.

### Host-Based Artifacts: 
* Created Windows Service: `mssecsvc2.0` | Display Name: Microsoft Security Center (2.0) Service
* Ransom File Extension: Appends `.WNCRY` to all successfully targeted files.
* Dropped Ransom Note: Creates text files named `@Please_Read_Me@.txt`. 

---

## Analyst Assessment 

WannaCry remains one of the most significant ransomware incidents in history due to its unique worm-like propagation. The attack demonstrated how unpatched vulnerabilities can rapidly escalate into global operational disruptions across critical infrastructures. Analyzing WannaCry provides a practical baseline for utilizing the MITRE ATT&CK framework to map adversary behavior, optimize detection rules, and reinforce organizational resilience.



