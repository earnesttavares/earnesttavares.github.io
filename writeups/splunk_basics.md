---
title: Splunk Basics
parent: Writeups
nav_order: 4
---

<h1 style="color:#33aaff;">Splunk Fundamentals: Log Analysis & Dashboard Visualization</h1>

<br>

<h2>Overview</h2>

In this lab, I explored **Splunk** Enterprise, a SIEM platform used to collect, index, search, and visualize security data. The goal of this lab was to gain hands-on experience with Splunk by ingesting Windows Event logs, investigating security events, and creating dashboards that visualize activity a SOC analyst might monitor. 

<br>

--- 

<h2>Technical Implementation</h2>

I configured Splunk to ingest Windows Event Logs and verified that the data was being indexed correctly through the Search & Reporting application. I explored how Splunk organizes data through indexes, sources, sourcetypes, and hosts, which helped me better understand how analysts filter, search, and investigate log data.

<br>

![Splunk Event Log Search](SplunkImage2.png)

*Figure 1: Querying Windows Event Logs in Splunk and reviewing indexed event data through the Search & Reporting application.*

<br>

I also validated data ingestion and indexing functionality by auditing internal Splunk operational logs (`index=_internal`) to confirm that events were being processed and stored correctly.

<br>

![Splunk Security Event Search](SplunkImage1.png) 

*Figure 2: Filtering Windows Security Event Logs in Splunk using SPL search queries.*

<br>

Using **Splunk Processing Language** (SPL), I performed statistical searches to analyze event activity and identify frequently occurring security events: 

```splunk
index=main sourcetype="wineventlog:security" | stats count by EventCode | sort -count
```

```splunk
index=main sourcetype="wineventlog:security" | stats count by EventType | sort -count
```

<br>

---

<h2>Dashboard Development</h2>

After identifying key log patterns, I built an interactive dashboard to turn raw log data into actionable security metrics. I converted the statistical query outputs into visual dashboard panels. 

<br>

* **<u>Security Event Distribution (by EventCode):</u>** Converted the `stats count by EventCode` search into a dashboard panel that visualizes event frequency by EventCode, helping identify the most frequently occurring security events.
* **<u>Authentication & Log Type Breakdown (by EventType):</u>** Leveraged the `stats count by EventType` dataset to create a pie chart visualization, providing a quick view of the distribution of event types within the collected Windows security logs. 

<br>

![Splunk Dashboard](SplunkImage3.png)

*Figure 3: Splunk dashboard visualizing Windows security event frequency and event type distribution.*

<br>

--- 

<h2>Key Concepts Learned</h2>

* **Splunk Enterprise Architecture:** Identifying and interpreting standard Windows metadata and event tracking structures.
* **Splunk Processing Language (SPL):** Utilizing statistical aggregation tools like `stats count by` and conditional sorting `sort -count` to transform raw logs into metrics.
* **Windows Security Auditing:** Exploring Windows Event Logs and interpreting security event data to better understand system activity and auditing records. 
* **Dashboard Creation & Data Visualization:** Building dashboards that turn raw log data into easily interpretable security metrics.
  
<br>

---

<h2>Skills Demonstrated</h2>

* Ingesting and indexing Windows Event Logs within Splunk.
* Writing SPL queries to aggregate and analyze security events.
* Investigating Windows security telemetry through event frequency and distribution analysis.
* Building dashboards to visualize and summarize security log data. 

<br>

---

<h2>Key Takeaways</h2>

This lab gave me hands-on experience with the core functions of a SIEM platform, from log ingestion and search to dashboard creation and visualization. By working with Windows Event Logs and writing SPL queries, I learned how security teams can transform raw event data into meaningful insights that support monitoring, investigation, and incident response. 


