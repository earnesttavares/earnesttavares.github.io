---
title: Wireshark Analysis 
parent: Labs
nav_order: 2
---

<h1>
  <span style="color: #D3D7CF;">Wireshark Traffic Analysis</span>  
  <span style="color: #3465A4;">& Incident Investigation Lab Report °‧ 𓆝 𓆟 𓆞</span> 
</h1>

<br>

<h2>Objective</h2>

The purpose of this lab was to develop hands-on experience using Wireshark to capture, filter, and analyze network traffic. The lab focused on understanding network protocols, identifying communication patterns, investigating suspicious activity, and extracting Indicators of Compromise (IoCs) from packet capture (PCAP) files. The lab also demonstrated how SOC analysts use Wireshark during incident response and forensic investigations. 

<br>

---

<h2>Tools & Software</h2>

* Wireshark Network Protocol Analyzer

* PCAP files provided in the lab exercises

* Wireshark Display Filters (e.g., ip addr, http, smtp) & TCP Stream Reassembly 

* Wireshark Statistics Menu for protocol breakdowns and endpoint traffic

<br>

---

<h2>Background</h2>

Wireshark is a network protocol analyzer commonly used for troubleshooting, security analysis, and network performance monitoring. It allows analysts to inspect live or captured traffic at the packet level and examine communications occurring across a network. Through filtering, visualization, and protocol analysis, Wireshark enables investigators to identify normal operations as well as suspicious or malicious activity. 

The lab emphasized understanding packet structure, TCP communications, filtering techniques, and the role of network traffic analysis in cybersecurity investigations. 

<br>

---

<h2>🪸 Part 1: Protocol & Network Traffic Analysis</h2>

<br>

<h3 style="color:#FF4040;">Packet & Protocol Examination 🦈</h3>

During the first phase of the lab, packet captures were analyzed to understand how network traffic flows between endpoints. Wireshark's three primary interface panes were examined: 

1. Packet List Pane
2. Packet Details Pane
3. Packet Bytes Pane

These components allowed detailed inspection of packet metadata, protocol information, and raw hexadecimal data. 

<br>

---

<h3 style="color:#FF4040;">Traffic Statistics 🦈</h3>

Analysis of the provided capture revealed: 

| **Metric** | **Result** | 
| :--- | :--- | 
| Total Subset of TCP Packets | 23,831 | 
| Packets Originating from Employee IP | 10,562 | 
| Packets Destined for Employee IP | 24,625 | 
| Total Packets in PCAP File Associated with Employee IP Address | 98.6% | 
| TCP Port 80 Packets | 897 | 
| TCP Communication Issues | 1,089 | 

These statistics were obtained using protocol filters, packet counting, and protocol hierarchy analysis. 

<br>

---

<h3 style="color:#FF4040;">HTTP User-Agent Analysis 🦈</h3>

Inspection of HTTP request traffic revealed details regarding the compromised workstation's operating environment. 

<br>

<u>Operating System:</u> Microsoft Windows <br>
<u>Browser Version:</u> Google Chrome 80 <br>

<br>

The User-Agent string provided evidence about the source operating system and web browser used by the endpoint during communications. 

<br>

---

<h3 style="color:#FF4040;">TCP Stream Analysis 🦈</h3>

TCP stream analysis was performed using the "Follow TCP Stream" feature. This allowed reconstruction of complete conversations between network endpoints, providing visibility into application-layer communications and session contents. 

Frame analysis revealed a frame length of 66 bytes within the selected TCP stream, which represents empty TCP ACKs or keep-alive packets observed during the exfiltration session. Additionally, multiple retransmissions and communication anomalies were observed during packet inspection. 

<br>

---

<h2>🪸 Part 2: Incident Response Investigation</h2>

<br>

<h3 style="color:#FF4040;">Scenario Overview 🦈</h3>

The lab simulated a cybersecurity incident involving an employee who clicked a malicious link. This action resulted in the installation of spyware on her workstation. The malware established unauthorized access and enabled attackers to: 

* Record browser activity and credentials

* Copy stored documents and files

* Monitor communications

* Exfiltrate sensitive information

The objective was to investigate network traffic and identify IoCs associated with the attack. 

<br>

---

<h3 style="color:#FF4040;">IoCs Identified 🦈</h3>

<u>INCIDENT START TIME</u>

The earliest suspicious activity observed in the packet capture occurred at: 

```text
22:51:00 UTC
```

This timestamp was identified by examining the arrival time of the first suspicious packet in the capture.   

<br>

---

<h3 style="color:#FF4040;">Victim System Information 🦈</h3>

| **IOC** | **Value** | 
| :--- | :--- | 
| IP Address | 192.168.1.27 | 
| MAC Address | bc:ea:fa:22:74:fb | 
| Hostname | DESKTOP-WIN11PC | 
| Username | admin@windows11users.com | 

<br>

---

<h3 style="color:#FF4040;">Suspicious Email Activity 🦈</h3>

Analysis of SMTP traffic revealed suspicious communications. 

| **Field** | **Value** | 
| :--- | :--- | 
| From Address | marketing@transgear.in | 
| To Address | zaritkt@arhitektondizajn.com | 

The destination address appeared anomalous compared to expected organizational communications and was identified as a likely exfiltration channel used by the spyware to transmit stolen information. 

<br>

---

<h3 style="color:#FF4040;">Hardware Information Exfiltrated 🦈</h3>

The spyware transmitted details about the victim's workstation including: 

| **Artifact** | **Value** | 
| :--- | :--- | 
| CPU | Intel Core i5-13600k | 
| RAM | 32 GB | 

<br>

---

<h3 style="color:#FF4040;">Credential Theft 🦈</h3>

Investigation of SMTP traffic revealed stolen account information. 

Compromised credential categories included: 

* Usernames

* Passwords

The malware collected and transmitted stored login information from the compromised workstation. 

<div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; margin: 20px 0;">
  
<div style="width: 45%; text-align: center;">
    <img src="/labs/images/wireshark_example_1.png" alt="Wireshark Example 1" style="width: 100%; height: auto; border: 1px solid #444; border-radius: 4px;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 8px; text-align: left; line-height: 1.4;">
      Figure 1: Analysis of the SMTP TCP stream revealed that the malware collected and transmitted system information and user credentials to an external email account. The transmitted data included host details, browser-stored credentials, and application account information.   
    </span>
  </div> 
  
  <br>
  <br>
  
  <div style="flex: 1; min-width: 280px; text-align: center;">
    <img src="/labs/images/wireshark_example_2.png" alt="Wireshark Example 2" style="width: 100%; border: 1px solid #444;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 5px;">Figure 2: Sensitive usernames and passwords have been redacted. Multiple online services appeared within the data, indicating that credentials stored in browsers and email clients were harvested before transmission. </span>
  </div>
</div>

<br>

---

<h3 style="color:#FF4040;">Encoded Authentication Data 🦈</h3>

Further inspection uncovered Base64-encoded authentication credentials associated with webhostbox.net. 

❖ **Decoded username:** 

* marketing@transgear.in

❖ **Decoded password:** 

* M@ssw0rd#621

These credentials were recovered by inspecting SMTP authentication data and decoding the captured Base64-encoded values.  

<br>

--- 

<h2>Analysis & Findings</h2>

The investigation demonstrated how network traffic analysis can be used to identify both system-level and user-level IoCs. Through protocol filtering, TCP stream reconstruction, SMTP analysis, and credential inspection, network evidence indicated a spyware infection associated with credential theft and data exfiltration activity. The investigation identified multiple IoCs, recovered encoded authentication data, and mapped attacker communications to suspicious outbound email activity. 

<br>

Key findings included: 

* Detection of the victim device's IP and MAC addresses.

* Identification of the compromised user account.

* Discovery of outbound malicious email communications.

* Confirmation of stolen credentials and sensitive system information.

* Recovery of encoded authentication credentials used by the attacker.

* Recognition of data exfiltration techniques operating over standard email protocols.

<br>

---

<h2>Skills Demonstrated</h2>

* Wireshark Packet Analysis

* TCP/IP Investigation

* HTTP Traffic Analysis

* SMTP Traffic Analysis

* IOC Identification

* Credential Theft Detection

* Malware Traffic Investigation

* Network Forensics

* Incident Response

* SOC Workflows 

<br>

---

<h2>💡 Key Takeaways</h2>

This lab provided practical experience using Wireshark for both network troubleshooting and cybersecurity incident response. The first portion strengthened understanding of packet analysis, protocol filtering, TCP communications, and network behavior. The second portion simulated a real-world security incident where packet analysis enabled the identification of malicious activity, compromised credentials, and multiple IoCs. 

Overall, the lab demonstrated how Wireshark serves as a critical tool for SOC analysts, incident responders, and cybersecurity professionals when investigating network-based attacks and determining the scope of compromise. 

