---
title: Volatility3 Basics 
parent: Labs
nav_order: 4
--- 

<h1 style="color:#33aaff;">Volatility 3 Memory Analysis</h1>

<br>

<h2>Overview</h2>

This lab introduced digital forensics and incident response (DFIR) concepts through the analysis of a compromised workstation using **Volatility 3**. The objective was to investigate evidence of a cyberattack, identify suspicious processes, and extract forensic artifacts directly from RAM (random access memory). 

Unlike traditional disk forensics, memory analysis provides insight into a system's live state, including running processes, command history, open handles, network activity, and credential artifacts that may never be written to disk. 

<br>

<h2>Objectives</h2>

* Understand the role of memory forensics in incident response.

* Install and configure Volatility 3.

* Analyze a Windows memory dump.

* Identify suspicious processes and artifacts.

* Investigate evidence related to attacker activity.

* Practice using Volatility 3 plugins commonly used during forensic investigations.

<br> 

---

<h2>Key Concepts</h2>

<details markdown="block"><summary>Click to Expand</summary>

<br>

**Digital Forensics & Incident Response (DFIR)** <br>

DFIR combines forensic investigation techniques and incident response processes to identify, analyze, contain, and respond to cybersecurity incidents. Memory analysis is a critical DFIR capability because it provides visibility into a system's live state and can reveal evidence that may not be present on disk. <br>

<br> 

**Volatility 3** <br>

Volatility 3 is an open-source memory forensics framework used to analyze RAM captures and recover information about processes, handles, network activity, credentials, and other forensic artifacts. <br>

<br>

**RAM** <br>

Random Access Memory (RAM) is volatile storage that temporarily holds data used by running applications and operating system processes. Because RAM captures a system's live state, it often contains evidence that may not exist on disk. <br>

</details>

<br>

---

<h2>Tools & Software</h2>

* Volatility 3

* Ubuntu Linux VM

* Windows Memory Image (victim.raw)

* Google Drive (artifact download)

* Terminal / Command Line

<br>

---

<h2>Scenario</h2>

A SOC team detected suspicious activity involving an employee account at a fictional organization. Evidence suggested that an attacker had gained unauthorized access and potentially moved through the environment using valid credentials. 

As a member of the Digital Forensics & Incident Response (DFIR) team, the task was to analyze a captured memory image and determine what activity occurred on the compromised workstation.

<br>

---

<h2>Investigation Walkthrough</h2>

<br>

<h3>💾 Environment Setup</h3>

Downloaded the memory file and prepared the analysis environment:

```shell
gdown https://drive.google.com/uc?id=1JKIxpj6q4_8rxcuNxYym5HpkfiJ2LISK # Downloaded the memory image

git clone https://github.com/volatilityfoundation/volatility3.git # Cloned the Volatility 3 repository

mv victim.raw volatility3 # Moved the memory image into the Volatility workspace

cd volatility3 # Navigated to the project directory 
```

Verified available Volatility3 plugins: 

```shell
python3 vol.py -h # Reviewed available plugins (-h is for help) 
```

Launched Volatility 3 with the appropriate plugin(s):

```shell
python3 vol.py -f [ImageName] [InsertPlugin]
```

<br>

---

<h2>Investigation Process</h2>

<h3>💾 Determining Memory Capture Time</h3>

To identify when the memory image was captured, I enumerated running processes using the `pslist` plugin: 

```shell
python3 vol.py -f victim.raw windows.pslist
```

The system timestamp contained within the memory image was: 

```text
SystemTime: 2019-05-02 18:11:45
```

This timestamp established the point-in-time context for the investigation. 

<br>

<h3>💾 Investigating Suspicious Processes</h3>

The lab directed attention toward PID 1600 as part of the investigation workflow. To gather additional information, I inspected the process handles associated with the process:   

```shell
python3 vol.py -f victim.raw windows.handles --pid 1600
```

The process running under PID 1600 was: 

```text
VBoxTray.exe
```

VBoxTray.exe is a legitimate VirtualBox Guest Additions process. This step demonstrated how analysts examine processes and associated artifacts when determining whether activity is benign or potentially malicious. 

<br>

<h3>💾 Identifying Exploitation Evidence</h3>

The attack scenario referenced exploitation activity associated with: 

```text
CVE-2019-2721
```

Researching vulnerabilities during a forensic investigation helps analysts understand attacker techniques, potential impact, and likely IoCs. 

<br>

<h3>💾 Credential Artifact Analysis</h3>

Recovered credential-related artifacts from memory using Volatility's `Hashdump` plugin to demonstrate how password hashes and privileged account data can remain accessible in RAM.   

```shell
python3 vol.py -f victim.raw windows.hashdump.Hashdump
```

The hash dump plugin demonstrated how password hash information can be recovered from memory, highlighting the importance of protecting privileged accounts and limiting credential exposure. 

<br>

<h3>💾 Command History Review</h3>

Examined command-line activity to better understand actions performed on the system. 

```shell
python3 vol.py -f victim.raw windows.cmdline.CmdLine
```

This plugin can help uncover attacker actions by revealing executed programs, scripts, and administrative commands. The command history provided insight into activity performed on the system and demonstrated how memory forensics can reveal evidence of user and attackers' actions that may not be immediately visible through disk analysis alone.

<br> 

---

<h2>Key Investigative Findings</h2>

 * Determined the memory image timestamp was 2019-05-02 18:11:45.
 
 * Investigated process PID 1600 (VBoxTray.exe) and reviewed associated handles.
 
 * Examined attack scenario evidence related to CVE-2019-2721.
 
 * Recovered credential-related artifacts using Volatility's Hashdump plugin.
 
 * Reviewed command-line history to better understand activity on the system.  

<br>

---

<h2>Skills Demonstrated</h2>

* Memory Forensics

* Volatility 3 

* Digital Forensics & Incident Response (DFIR)

* Linux Command Line

* Process Analysis

* Threat Investigation 

* Credential Artifact Investigation

* Incident Response 

* Cybersecurity Documentation

<br>

---

<h2>Takeaways</h2>

This investigation demonstrated how memory forensics supports incident response by providing visibility into a system's live state. Using Volatility 3, I analyzed running processes, examined process handles, reviewed command-line activity, and explored credential-related artifacts contained within RAM. The exercise reinforced the importance of memory analysis as a DFIR technique, particularly when critical evidence may not be present on disk. 
