---
title: Attack & Defend AWS
parent: Workshops
nav_order: 1
---

<h1>
  <span style="color: #FF9900;">Attacking & Defending AWS:</span>  
  <span style="color: #FFFFFF;">A Cloud Security Breach, Start to Finish</span> 
</h1>

<br> 

> ### Workshop Details 
> - **Focus:** Offensive, Defensive & Incident Response in the cloud
> - **Environment:** AWS (EC2, CloudTrail, CloudWatch, Lambda, DynamoDB)
> - **Platform:** TryHackMe Community Event
> - **Completed:** 15 September 2026
>
> - **[Click to View Certificate](/assets/certificates/thm-aws-cloud-breach.png)**

<br>
<br>

<h2>Overview</h2>
I participated in a hands-on cloud security workshop focused on the lifecycle of a cloud breach within an AWS environment. The exercise simulated a real-world attack against a vulnerable web application, allowing me to experience both the attacker's and the defender's perspectives. 

<br>

---

## Index 🕮

<details>
<summary><b>AWS Identity & Access Management</b></summary>

<br>

- AWS IAM user: An AWS identity representing a person or application with *long-term credentials*. Users can authenticate using passwords or access keys. <br>

<br>
  
- AWS IAM Role: An AWS identity that provides *temporary credentials* for temporary access via the AWS Security Token Service (STS). AWS services, EC2 instances, applications, and users assume roles to obtain temporary access to resources. Can be shared with multiple people or applications over time. <br>
</details> 


<details>
<summary><b>Vulnerabilities & Exploits</b></summary>

<br>
  
- SSRF (Server-Side Request Forgery): A web security vulnerability that allows an attacker to cause a server to make unintended requests on behalf of the attacker. This exploit targets internal infrastructures, firewalled systems, and cloud metadata endpoints. <br>

<br>

- IMDS (Instance Metadata Service): A feature available on EC2 instances that provides information about the instance, including network configuration, security groups, and IAM role credentials. It is accessible only from within the instance at `http://169.254.169.254/latest/meta-data/`. <br>

<br>

- IMDSv1: The original version of the IMDS. Vulnerable to SSRF attacks because an attacker can steal credentials with a simple, HTTP `GET` request. <br>

<br>

- IMDSv2: Improved version of IMDS. Stops SSRF attacks by requiring a valid session token before metadata can be accessed. <br>  
</details>


<details>
<summary><b>AWS Services Used</b></summary>

<br>

- AWS CloudTrail: A logging and auditing service that records the actions performed within an AWS account, including API calls made by users, roles, and AWS services. Helps to answer the 'who' performed an action, 'what' action occurred, and 'when' it happened. <br>

<br>

- AWS CloudWatch: Monitoring service responsible for (Metrics, Logs, Alarms, Dashboards) and is used to monitor application and infrastructures performance. <br>

<br>

- AWS Lambdas: Serverless service that allows users to run code without worrying about server management. <br>

<br>

- AWS DynamoDB: Fully managed database service. <br>

<br>

- AWS EC2 (Elastic Compute Cloud) Instance: A virtual computing environment in the cloud that allows users to configure and run scalable applications on AWS infrastructure. <br>

<br>

- AWS CloudShell: Fully managed Linux shell environment that provides authenticated command line access to AWS resources and tools. <br>

<br>

- Security Groups: Virtual firewalls that control inbound and outbound traffic for AWS resources such as EC2 instances. Security groups are stateful and operate alongside network ACLs within a Virtual Private Cloud (VPC). <br>     
</details>

<br>

---

<p align="center">
  <img src="/workshops/images/attack_path.png" alt="Attack Path" width="80%">
  <br>
  <em style="color: #888888; font-size: 0.9em;">Figure 1: Attack path showing how an SSRF vulnerability can be exploited to gain access to AWS services and sensitive data.</em>
</p> 

---

## Objectives

| **🔴 Attacker's Perspective** | **🔵 Defender's Perspective** | 
| :--- | :--- | 
| Identify vulnerabilities in the company's web application environment. | Analyze attacker activity using CloudTrail Event History, CloudWatch logs, and Lambda code. |
| Exploit a Server-Side Request Forgery (SSRF) vulnerability to manipulate front-facing application inputs. | Apply containment, eradication, and remediation actions. | 
| Access EC2 Instance Metadata Service (IMDS) to extract instance role credentials & use those credentials to extract data from the company's database. | Restore services while validating mitigations. | 

<br>

---

<h2 style="color:#FF0000;">🔴 The Attacker</h2>

The workshop began with reconnaissance of the CloudFactory environment. CloudFactory is a company that operates an AWS-hosted web app used to store "cloud formulas". The environment serves as the target infrastructure for this workshop. In the publicly accessible web application, I inspected its client-side JavaScript source code (`app.js`) using standard browser developer tools. During this process, I identified an unsanitized input variable within the application's *Cloud Skin* preview feature that passed strings directly to the backend server. This input will be leveraged to perform SSRF requests against the EC2 Instance Metadata Service (IMDS): 

<br>

<details markdown="block"><summary>View Code</summary>
  
```javascript
var url = document.getElementById("skinUrl").value;
// Retrieves the URL entered by the user in the "skinUrl" input field and stores it in the variable 'url'. 

"CloudFactoryGenerateFunction", { action: "list" })
// Parameter set to "list", triggering a server-side listing operation. 

awsLambdaInvoke(functionName, payload)
// Invokes the specified AWS Lambda function and sends the provided payload for processing.
```
</details>

<br>

Using this vulnerable input field, I queried the EC2 Instance Metadata Service (IMDS) at `http://169.254.169.254/latest/meta-data/`, which returned a list of available metadata directories associated with the virtual machine. This confirmed that the application could issue requests to the EC2 Instance Metadata Service on behalf of an attacker, validating the SSRF vulnerability. Querying `http://169.254.169.254/latest/meta-data/iam/security-credentials/` exposed an IAM role attached to the EC2 instance. Because IMDSv1 was enabled, temporary IAM role credentials could be retrieved without the authentication tokens enforced by IMDSv2. I then utilized AWS CLI to authenticate the stolen credentials and successfully perform a full database dump. 

<br>

<details markdown="block"><summary>View Images</summary>
  
<div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; margin: 20px 0;">
  
<div style="width: 45%; text-align: center;">
    <img src="/workshops/images/web_application_code.png" alt="Reviewing Web Application Code Using Inspect Tool" style="width: 100%; height: auto; border: 1px solid #444; border-radius: 4px;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 8px; text-align: left; line-height: 1.4;">
      Figure 2: Unsanitized input and dead code pointing to a backend Lambda function susceptible to SSRF.
    </span>
  </div> 
  
  <br>
  <br>
  
  <div style="flex: 1; min-width: 280px; text-align: center;">
    <img src="/workshops/images/lamba_invocation.png" alt="Access to Sensitive Data Using Temporary Credentials" style="width: 100%; border: 1px solid #444;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 5px;">Figure 3: Exploited an SSRF vulnerability to retrieve EC2 metadata (IMDSv1) and temporary AWS credentials. Using AWS CloudShell, the temporary credentials were leveraged to invoke a Lambda function and gain access to CloudFactory's sensitive data stored in DynamoDB.</span>
  </div>
</div>
</details>

---

<h2 style="color:#5C5CFF;">🔵 The Defender</h2>

After completing the attack path, I transitioned into the role of a cloud security analyst investigating the incident. 

<br>

I reviewed the CloudWatch dashboard and found 2 alerts: 
1. Reconnaissance activity alert - (GetCallerIdentityReconCount)
2. Lambda invocation outside of the EC2 alert - (LambdaInvokeOutsideEc2Count) 

<br>

Further investigation identified: 
* Lambda's payload parameter value, `list` allowed for a full database dump.
* EC2 instance metadata settings permitting IMDSv1.
* Evidence of credential abuse and unauthorized Lambda execution.

<br>

Correlating CloudWatch logs, CloudTrail event history, Lambda code, and a purpose-built alert enabled identification of the attack path, validation of credential abuse, and remediation actions to prevent additional data exfiltration. 

<br>

<details markdown="block"><summary>View Images</summary>
  
<div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; margin: 20px 0;">
  
<div style="width: 45%; text-align: center;">
    <img src="/workshops/images/cloudwatch_dashboard.png" alt="Two Alerts on CloudWatch Dashboard" style="width: 100%; height: auto; border: 1px solid #444; border-radius: 4px;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 8px; text-align: left; line-height: 1.4;">
      Figure 4: Lambda invocation activity outside the expected scope and GetCallerIdentity reconnaissance alerts indicated a potential security breach. 
    </span>
  </div> 
  
  <br>
  <br>
  
  <div style="flex: 1; min-width: 280px; text-align: center;">
    <img src="/workshops/images/insecure_code_block.png" alt="Correlation Between the Attacker and Insecure Code Block" style="width: 100%; border: 1px solid #444;">
    <span style="display: block; color: #888; font-size: 0.85em; margin-top: 5px;">Figure 5: The defender identified how the attacker exploited the application to access the database. To eradicate the threat, IMDSv2 was enabled and the vulnerable Lambda function code path was disabled. Recovery involved restored the HTTPS rule in the instance security group.</span>
  </div>
</div>
</details>

---

<h2 style="color:#FF9900;">Incident Response</h2>

<u>Containment</u> 

* Edited inbound rules in Instance Security Group that removed public HTTPS access to prevent further interaction with the vulnerable application.
  
* Reduced the attack surface while preserving the environment for investigation.
  
* **Recognized that removing public access stopped <u>new attacks</u> but did not invalidate already stolen temporary credentials.**

<br>

<u>Eradication</u>

* In the EC2 Instance Metadata Service, I configured the IMDSv2 settings from `optional` (allows both IMDSv1 & IMDSv2) to `required` (IMDSv2 only). This is to prevent future SSRF metadata exfiltration.
  
* Disabled the Lambda code branch that was responsible for the full database dump.
  
* These remediation actions eliminated the root causes of the attack chain. 

<br>

<u>Recovery</u>

* Restored HTTPS access rule in the Instance Security Group.
  
* Validated application functionality after restoring HTTPS access and confirmed that security controls remained effective.
  
* Confirmed that SSRF attacks could no longer retrieve instance credentials.

<br>

<p align="center">
  <img src="/workshops/images/imdsv2_enforced.png" alt="Vulnerabilities Resolved" width="80%">
  <br>
  <em style="color: #888888; font-size: 0.9em;">Figure 6: Following containment and recovery efforts, testing verified that the SSRF attack path was no longer exploitable after IMDSv2 enforcement.</em> 
</p> 

<br>

---

<h2>Key Skills Demonstrated</h2>

* AWS Cloud Security
* Incident Response
* CloudTrail
* CloudWatch
* IAM
* SSRF Analysis
* AWS Lambda
* AWS CLI

<br> 

--- 

<h2>What I Learned 💡</h2>

* How SSRF vulnerabilities and IMDSv1 can expose cloud metadata services.
  
* Why IMDSv2 significantly reduces credential theft risk.
  
* How various tools like CloudTrail, CloudWatch, and Lambda come together during investigations.
  
* The difference between containment, eradication, and recovery.

<br>

---

<h2>Key Takeaways</h2>

This workshop demonstrated how a single web application vulnerability can turn into a significant cloud breach. I gained practical experience following the attacker's path from initial exploitation through to data exfiltration. Equally valuable was the defender phase, where I investigated logs, validated alerts, identified root causes, and implemented corrective actions. The workshop reinforced the importance of secure cloud configurations, the principle of least privilege, sanitized inputs, and the distinction between containment (revoke sessions) and eradication (fix the misconfigurations) during the incident response cycle. Overall, the workshop provided practical experience investigating and remediating a cloud security incident from initial compromise to recovery. 
