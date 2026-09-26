---
title: Nmap & DVWA
parent: Labs
nav_order: 1
---

<h1 style="color:#33aaff;">Nmap Reconnaissance Against DVWA</h1>

<br>

<h2>Objective</h2>

Use Nmap to discover hosts, enumerate open ports and services, and identify potential security weaknesses on a DVWA (Damn Vulnerable Web Application) Docker container within a pre-configured Ubuntu VM.  

<br>

---

<h2>Tools & Software</h2>

⚙︎ Kali Linux / Ubuntu VM Terminal <br>
⚙︎ Nmap - Network Mapper <br>
⚙︎ DVWA - Damn Vulnerable Web Application <br>
⚙︎ Docker <br> 

<br>

---

<h2>Skills Demonstrated</h2>

⚙︎ Host Discovery <br>
⚙︎ Port Scanning <br>
⚙︎ Service Enumeration <br>
⚙︎ TCP & SYN Scanning <br>
⚙︎ Vulnerability Assessment <br>
⚙︎ Analysis of Nmap Scan Results <br>

<br>

---

<h2>Lab Setup</h2>

The DVWA application was hosted as a Docker container within the VM environment. The container was started and its IP address was identified prior to scanning. 

<br>

<h3>Starting DVWA</h3>

<br>

```shell
docker start dvwa
```

<br>

<h3>Obtain Target IP</h3>

Before performing a scan, the IP address of a DVWA container is needed. The following command was used to obtain the target IP: <br>

```shell
docker inspect dvwa --format
'{{.NetworkSettings.Networks.bridge.IPAddress}}'
```

<br>

The terminal will display the container's IP address, `172.17.0.2`.

<br>

---

<h3>Ping Sweep</h3>

A ping aka ICMP sweep was used to verify that the target was reachable. It is a network scanning technique used to determine which IP addresses are active and which ones are inactive within in a range: 

<br>

```shell
nmap -sP 172.17.0.2
```

<br>

Result: <br>
The scan confirmed that the DVWA host was online and responding to network probes. No port information was collected during this phase because `nmap -sP` only performs host discovery. 

<br>

---

<h3>SYN (Stealth) Scan</h3>

A SYN aka stealth scan was performed to identify open ports while avoiding a full TCP connection. It is a faster and more discrete method of identifying open ports because it does not complete the full TCP three-way handshake.  

<br>

```shell
sudo nmap -sS 172.17.0.2
```

<br>

Result: <br>
The scan successfully identified open ports and services. Because the target was operating as a Docker container, the MAC address appeared with an unknown vendor designation `UNKNOWN`, suggesting a virtualized or containerized environment. 

<br>

--- 

<h3>Aggressive Scan</h3>

An aggressive scan was executed to gather detailed information about the target such as OS detection, service versioning, and script scanning. 

<br>

```shell
sudo nmap -A 172.17.0.2
```

<br>

**Information Gathered from Scan:** 

🔵 Open ports <br>
🔵 Running services <br>
🔵 Service versions <br>
🔵 HTTP security details <br>
🔵 MAC address information <br>
🔵 OS detection attempts <br>
🔵 Network distance estimation <br>

<br>

**Findings:**

| Item | Result | 
| :--- | :--- | 
| Open Port | 80 | 
| Service | HTTP | 
| Version | Apache/2.4.25 | 
| OS Detection | Inconclusive | 
| Network Distance | Local Docker network (1 hop) | 

<br>

Result: <br>
Port 80 was identified as the only accessible service, running Apache/2.4.25 on Debian. OS fingerprinting was inconclusive due to the containerized environment. This is common with containerized hosts. 

<br>

---

<h3>Vulnerability Scan</h3>

<br>

Nmap's vulnerability scripts were used to identify potential weaknesses. 

<br>

```shell
sudo nmap --script vuln 172.17.0.2.
```

<br>

Potential security concerns identified: 
* PHPSESSID cookie missing the httponly flag.
* Outdated Apache 2.4.25 web server.
* `robots.txt` disclosing potentially sensitive application paths.

<br>

Nmap did not directly confirm vulnerabilities such as SQL injection, CSRF, or XSS, but the findings provided potential security risks. The outdated web server may contain publicly known CVE's and the `robots.txt` discloses directory paths that may reveal sensitive areas of the application.  

<br>

---

<h3>WAF Detection</h3>

A scan was conducted to determine if a WAF was protecting the DVWA instance.

<br>

```shell
sudo nmap --script http-waf-detect 172.17.0.2
```

<br>

**Result:** <br>
The scan indicated the possible presence of a WAF, IDS, or IPS protecting the DVWA application. Additional validation would be required to confirm the detection.  

<br>

---

<h2>Key Takeaways</h2>

* Successfully performed host discovery and service enumeration using Nmap.

* Identified accessible services running on the DVWA container.

* Gathered service version information through aggressive scanning techniques.

* Discovered potential security concerns involving session cookie security, service version exposure, and information disclosure through `robots.txt`.

* Gained practical experience using Nmap for reconnaissance, enumeration, and vulnerability assessment within a controlled lab environment. 
