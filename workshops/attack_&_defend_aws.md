---
title: Attack & Defend AWS
parent: Workshops
nav_order: 1
---

<h1 style="color:#33aaff;">Attacking & Defending AWS: A Cloud Security Breach from Start to Finish</h1> 

<br> 

**<u>Workshop Type:</u>** Cloud Security, AWS Incident Response, Offensive & Defensive Security <br>
**<u>Platform:</u>** TryHackMe Community Event <br>
**<u>Date Completed:</u>** 15 September 2026 <br>
**<u>Certificate 🏅:</u>** `/assets/certificates/thm-aws-cloud-breach.png` 

<br>

---

<h2>Overview</h2>

I participated in a hands-on cloud security workshop focused on the complete lifecycle of a cloud breach within an AWS environment. The exercise simulated a real-world attack against a vulnerable web application, allowing me to experience both the attacker's and the defender's perspective. The workshop followed a breach from initial reconnaissance down to exploitation, detection, containment, eradication, and recovery. 

<br>

---

<h2>Workshop Objectives</h2>

* Identify vulnerabilities in an AWS-hosted web application environment, CloudFactory.
* Exploit a Server-Side Request Forgery (SSRF) vulnerability to manipulate front-facing application inputs.
* Access EC2 Instance Metadata Service (IMDS).
* Demonstrate the risks of IMDSv1.
* Analyze attacker activity using CloudWatch, CloudTrail, and Lambda execution logs.
* Apply containment and remediation actions.
* Restore services while validating mitigations.

<br>

---

<h2>Quick Breakdown</h2>

* **CloudFactory** = The company
* **AWS Account** = The company's cloud environment
* **EC2 Instances** = Virtual machines the company runs
* **IAM roles/users** = Identities and permissions within the environment 

<br>

---

<h2 style="color:#ff3333;">Attacker Perspective</h2>

The workshop began with reconnaissance of the CloudFactory environment. I analyzed the publicly accessible web application frontend and inspected its client-side Javascript source code (`app.js`) using browser developer tools. During this process, I identified an unsanitized input variable within the application's *Cloud Skin* preview feature that passed strings directly to the backend. This could be abused for Server-Side Request Forgery (SSRF): 

```text
var url = document.getElementById("skinUrl").value;

"CloudFactoryGenerateFunction", { action: "list" })

awsLambdaInvoke(functionName, payload) 
```

Using the vulnerable input field, I directed server-side requests toward the EC2 Instance Metadata Service (IMDS) at `169.254.169.254`, confirming unauthorized access to instance metadata. This exposed an IAM role associated with the EC2 instance. Because the environment permitted IMDSv1 requests, metadata could be retrieved without authentication tokens.

Through the workshop exercise, I demonstrated how access to instance metadata could lead to exposure of AWS credentials. Using AWS CLI commands, I enumerated available permissions and analyzed how an attacker could leverage compromised credentials to interact with connected AWS services. Source code analysis revealed a debugging code path that could be triggered through the Lambda function, enabling unauthorized access to DynamoDB data. This illustrates how an attacker with Lambda invocation privileges can exploit internal function logic to bypass standard controls and achieve unauthorized data exposure.  

<br>

---

<h2 style="color:#33eeff;">Defender Perspective</h2>

After completing the attack path, I transitioned into the role of a cloud security analyst investigating the incident. 

<br>

I reviewed CloudWatch dashboards and alerts configured to detect: 
* AWS `STS GetCallerIdentity` reconnaissance activity
* Lambda invocations originating from non-approved or non-instance IP addresses <br>

<br>

Through CloudWatch alerts, CloudTrail logs, Lambda execution records, and source code review, I reconstructed the complete attack chain. Alerts for AWS `STS GetCallerIdentity` activity and Lambda invocations from a non-instance IP provided the initial indicators of compromise. Analysis showed that temporary IAM credentials obtained through the SSRF attack were used from an external IP address to invoke a vulnerable Lambda function and expose DynamoDB data. 

<br>

Further investigation identified: 
* A leftover debugging code path (debug/list) that allowed unrestricted DynamoDB data retrieval.
* EC2 instance metadata settings permitting IMDSv1.
* Evidence of credential abuse and unauthorized Lambda execution. 

<br>

---

<h2 style="color:#ffeb33;">Incident Response</h2>

**<u>Containment</u>** 

* Removed public HTTPS access through the EC2 Security Group.
* Prevented further interaction with the vulnerable application.
* Reduced the attack surface while preserving the environment for investigation.
* Recognized that removing public access stopped new attacks but did not invalidate already stolen temporary credentials. 

<br>

**<u>Eradication</u>**

* Enforced IMDSv2 requirements on the EC2 instance.
* Disabled the Lambda code branch responsible for database dumping.
* Eliminated the root causes of the attack chain.

<br>

**<u>Recovery</u>**

* Restored HTTPS access after remediation.
* Verified that metadata access attempts were blocked.
* Confirmed that SSRF attacks could no longer retrieve instance credentials.

<br>

---

<h2>Key Skills Demonstrated</h2>

* AWS Security Fundamentals
* Server-Side Request Forgery (SSRF)
* IAM Role Analysis
* EC2 Instance Metadata Service (IMDS)
* AWS Lambda Security
* CloudWatch Alert Investigation
* CloudTrail Analysis
* Cloud Incident Investigation
* Incident Response Methodology
* Containment and Remediation
* AWS CLI Usage

<br> 

--- 

<h2>Key Takeaways</h2>

This workshop demonstrated how a single web application vulnerability can turn into a significant cloud security incident. I gained practical experience tracing an attack path from SSRF exploitation to credential theft and eventual unauthorized data access. Equally valuable was the defender phase, where I investigated logs, validated alerts, identified root causes, and implemented corrective actions. The workshop highlighted techniques for limiting the impact of compromised temporary credentials and preventing their continued misuse. The exercise reinforced the importance of secure cloud configurations, least-privilege design, visibility through logging, and the distinction between containment and eradication during the incident response cycle. 
