---
title: RITA Analysis
parent: Labs
nav_order: 3
---

<h1 style="color:#33aaff;">RITA Analysis</h1>  

<br>

<h2>Objective</h2>

Use RITA (Real Intelligence Threat Analytics) to analyze Zeek network logs and identify indicators of malicious activity, including C2 (command and control) beaconing and DNS tunneling. The goal was to investigate suspicious network communications, identify potential indicators of compromise, and understand how threat actors can leverage legitimate services for covert communications. RITA is specifically designed to detect behaviors including beaconing, long-lived connections, blacklisted domains, and DNS tunneling. 

<br>

---

<h2>Tools & Software</h2>

* RITA

* MongoDB

* Zeek Logs

* Linux Terminal

* zgrep

<br>

---

<h2>Key Concepts</h2>

<details markdown="block"><summary>Click to Expand</summary>

<br>

**Beacon Detection** <br>

Beaconing occurs when an infected host communicates with a C2 server at regular intervals. This recurring communication pattern is commonly used by malware operators to maintain persistence and receive instructions from remote infrastructure. RITA analyzes traffic metadata to identify these patterns. <br>

<br>

**DNS Tunneling** <br>

DNS tunneling is a technique that allows attackers to transmit data through DNS requests and responses. Because DNS traffic is often trusted and allowed through perimeter defenses, attackers can abuse it to create covert communication channels for C2 or data exfiltration. 

<br>

**Real Intelligence Threat Analysis** <br>

RITA (named after Rita Strand) is an open-source network traffic analysis framework developed by Active Countermeasures. It identifies suspicious communication patterns such as beaconing, long-duration connections, and DNS tunneling activity. 

<br>

**MongoDB** <br>

MongoDB stores the imported Zeek log data that RITA analyzes during threat hunting investigations. 

<br>

**zgrep** <br>

zgrep enables searching within compressed log files, allowing investigators to review large datasets without extracting them first. 

</details>

<br>

---

<h2>Analysis & Investigation</h2>

<h3>🔶Imported & Analyzed Network Traffic</h3>

I imported the provided Zeek logs into RITA and generated reports to identify abnormal communication patterns across the network. I reviewed the Beaconing, User Agent, and DNS analysis reports to locate activity that deviated from normal traffic behavior. 

<br>

<h3>🔶Investigated Beaconing Activity</h3>

The Beaconing report identified a suspicious external IP address: 

```text
138.197.117.74
```

This host generated approximately 8,440 connections, making it the most suspicious entry within the Beacon Detection report. The host displayed a consistent communication pattern commonly associated with C2 infrastructure. 

Further investigation revealed the IP address was hosted by **DigitalOcean**, a cloud provider commonly used to host internet accessible services because it provides flexibility, scalability, and ease of deployment.

<br>

<h3>🔶Correlated User-Agent Activity</h3>

I reviewed the User Agent report and identified a large volume of traffic associated with the following User-Agent string: 

```text
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)
```

The connection count closely aligned with the beaconing activity, providing additional evidence that the traffic was related to the identified C2 communications. 

<br>

<h3>🔶Investigated DNS Traffic</h3>

Using a second dataset, I reviewed RITA's DNS report and identified excessive requests to: 

```text
cat.nanobotninjas.com
```

The domain generated approximately 82,920 DNS requests, making it a strong candidate for malicious activity. 

<br>

<h3>🔶Examined DNS Logs with zgrep</h3>

To validate the findings, I used zgrep to search the compressed DNS logs for activity related to the suspicious domain: 

```text
zgrep "cat.nanobotninjas.com" dns.log.gz
```

The extracted records contained repeated DNS TXT record queries containing references to the internal host: 

```text
10.234.234.105
```

The frequency and structure of these requests suggested an attempt to transport data through DNS traffic rather than perform legitimate name resolution. 

<br>

<h3>🔶Identified DNS Tunneling Techniques</h3>

Reviewing the DNS requests showed that each query contained unique, random-looking hexadecimal strings prepended to the domain name: 

```text
# Example Structure
[random_hex_value].cat.nanobotninjas.com
```

These hexadecimal values served as encoded payload fragments. By continuously changing the subdomain value, the attacker avoided DNS caching mechanisms while transmitting information to the remote server. This behavior is a classic indicator of DNS-based C2 communication.  

Based on the observed behavior, the traffic was consistent with **DNSCat2**, a tool that uses DNS queries as a covert communication channel between an infected host and an attacker-controlled server. 

Evidence supporting this conclusion included: 

* Large volumes of DNS TXT record requests.

* Hexadecimal-encoded payload fragments.

* Repeated communication with a controlled domain.

* DNS-based C2 behavior.

* Anti-caching techniques through constantly changing subdomains.

These characteristics closely align with known **DNSCat2** traffic patterns. 

<br>

---

<h2>Evidence/Results</h2>

<h3>Beaconing Detection</h3>

| **Findings** | **Value** | 
| :--- | :--- | 
| Suspicious External IP | 138.197.117.74 | 
| Number of Connections | ~8,440 | 
| Hosting Provider | DigitalOcean | 
| Activity Type | C2 Beaconing | 

<br>

<h3>DNS Tunneling Investigation</h3>

| **Findings** | **Value** | 
| :--- | :--- | 
| Suspicious Domain | cat.nanobotninjas.com | 
| DNS Requests | ~82,920 | 
| Internal Host | 10.234.234.105 | 
| Record Type | TXT | 
| Suspected Tool | DNSCat2 | 

<br>

---

<h2>Key Findings</h2>

* Identified probable C2 beaconing activity.

* Discovered excessive DNS traffic associated with a single domain.

* Correlated suspicious DNS requests to an internal host.

* Observed encoded subdomains indicative of DNS tunneling.

* Attributed activity to behavior consistent with DNSCat2.

<br>

The analysis identified two significant malicious behaviors: 

1. C2 beaconing to a cloud-hosted server at 138.197.117.74.

2. DNS tunneling activity leveraging **DNSCat2** through the domain cat.nanobotninjas.com.

<br>

RITA successfully highlighted these anomalous communication patterns, while supplemental investigation using zgrep provided additional evidence supporting the DNS tunneling findings. Together, these tools enabled the detection of covert attacker communications that may have otherwise blended into normal network traffic. 

<br>

---

<h2>Skills Demonstrated</h2>

* Threat Hunting

* Network Traffic Analysis

* C2 Detection

* DNS Analysis

* Beaconing Detection

* Log Analysis

* Linux Command Line

* IOC (Indicator of Compromise) Identification

* Security Investigation

* RITA Framework

* Zeek Log Analysis

<br> 

---

<h2>Takeaway</h2>

This lab demonstrated how behavioral network analysis can reveal attacker communications that may evade traditional signature-based detection methods. Using RITA and Zeek data, I identified indicators of C2 beaconing, investigated suspicious DNS activity, and validated evidence of DNS tunneling through log analysis. The investigation reinforced the importance of combining automated analytics with manual review when conducting threat hunting operations. 
