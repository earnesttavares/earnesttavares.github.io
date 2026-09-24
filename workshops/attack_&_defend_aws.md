---
title: Attack & Defend AWS
parent: Workshops
nav_order: 1
---

<h1 style="color:#33aaff;">Attacking & Defending AWS: A Cloud Security Breach from Start to Finish</h1> 

<br> 

Workshop Type: Offensive Security, Defensive Security, and Incident Response in the Cloud <br>
Platform: TryHackMe Community Event <br>
Date Completed: 15 September 2026 <br>
[Certification](/assets/certificates/thm-aws-cloud-breach.png) 

<br>

---

<h2>Overview</h2>

I participated in a hands-on cloud security workshop focused on the lifecycle of a cloud breach within an AWS environment. The exercise simulated a real-world attack against a vulnerable web application, allowing me to experience both the attacker's and the defender's perspectives. 

<br>

---

<h2>Additional Context</h2>

<details markdown="block"><summary>Expand to View</summary>

* *CloudFactory* - 
* *IAM roles/users* -
* *Instances* - 
* *SSRF* - 
* *IMDSv1* - 
* *IMDSv2* - 
* *AWS EC2 Instances* -
* *CloudTrail* - 
* *CloudWatch* - 
* *Lambdas* - 
* *DynamoDB* -  
* *AWS CloudShell* -

</details>

<br>

---

<h2>Objectives</h2>

Attacker's Perspective:
* Identify vulnerabilities in the company's web application environment.
* Exploit a Server-Side Request Forgery (SSRF) vulnerability to manipulate front-facing application inputs.
* Access EC2 Instance Metadata Service (IMDS) to extract instance role credentials & use those credentials to extract data from the company's database.

<br>

Defender's Perspective: 
* Analyze attacker activity using CloudTrail Event History, CloudWatch logs, and Lambda code.
* Apply containment, eradication, and remediation actions.
* Restore services while validating mitigations.

<br>

---

<h2 style="color:#ff3333;">The Attacker</h2>

The workshop began with reconnaissance of the CloudFactory environment. In the publicly accessible web application, I inspected its client-side JavaScript source code (`app.js`) using standard browser developer tools. During this process, I identified an unsanitized input variable within the application's *Cloud Skin* preview feature that passed strings directly to the backend server. This input will be leveraged to perform SSRF requests against the EC2 Instance Metadata Service (IMDS): 

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

Using this vulnerable input field, I queried the EC2 Instance Metadata Service (IMDS) at `http://169.254.169.254/latest/meta-data/`, which returned a list of available metadata directories associated with the virtual machine. This confirmed that the application could issue requests to the EC2 Instance Metadata Service on behalf of an attacker, validating the SSRF vulnerability. Using `http://169.254.169.254/latest/meta-data/iam/security-credentials/` exposed an IAM role attached to the EC2 instance. Because IMDSv1 was enabled, temporary IAM role credentials could be retrieved without the authentication tokens enforced by IMDSv2. I then utilized AWS CLI to authenticate the stolen credentials and successfully perform a full database dump. 

<br>

---

<h2 style="color:#33eeff;">The Defender</h2>

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
---

<h2 style="color:#ffeb33;">Incident Response</h2>

**<u>Containment</u>** 

* Edited inbound rules in Instance Security Group that removed public HTTPS access to prevent further interaction with the vulnerable application.
* Reduced the attack surface while preserving the environment for investigation.
* **Recognized that removing public access stopped <u>new attacks</u> but did not invalidate already stolen temporary credentials.**

<br>

**<u>Eradication</u>**

* In the EC2 Instance Metadata Service, I configured the IMDSv2 settings from `optional` (allows both IMDSv1 & IMDSv2) to `required` (IMDSv2 only). This is to prevent future SSRF metadata exfiltration. 
* Disabled the Lambda code branch that was responsible for the full database dump.
* These remediation actions eliminated the root causes of the attack chain. 

<br>

**<u>Recovery</u>**

* Restored HTTPS access rule in the Instance Security Group.
* Validated application functionality after restoring HTTPS access and confirmed that security controls remained effective. 
* Confirmed that SSRF attacks could no longer retrieve instance credentials.

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

<h2>What I Learned</h2>

* How SSRF vulnerabilities and IMDSv1 can expose cloud metadata services.
* Why IMDSv2 significantly reduces credential theft risk.
* How various tools like CloudTrail, CloudWatch, and Lambda come together during investigations.
* The difference between containment, eradication, and recovery.

<br>

---

<h2>Key Takeaways</h2>

This workshop demonstrated how a single web application vulnerability can turn into a significant cloud breach. I gained practical experience following the attacker's path from initial exploitation through to data exfiltration. Equally valuable was the defender phase, where I investigated logs, validated alerts, identified root causes, and implemented corrective actions. The workshop reinforced the importance of secure cloud configurations, the principle of least privilege, sanitized inputs, and the distinction between containment (revoke sessions) and eradication (fix the misconfigurations) during the incident response cycle. Overall, the workshop provided practical experience investigating and remediating a cloud security incident from initial compromise through recovery. 
