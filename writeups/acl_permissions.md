---
title: ACLs & Permissions 
parent: Writeups
nav_order: 2
---

<h1 style="color:#33aaff;">ACLs & Permissions</h1> 

<span style="color:#D4A017;">*Access Control ✏ Linux Administration ✏ ACL Management ✏ Bash Automation*</span>  

<h2>Overview</h2>

Linux Access Control Lists (ACLs) provide granular control over file and directory permissions beyond traditional Linux permission models. In a simulated enterprise environment, I used ACLs, groups, and directory permissions to implement least-privilege access controls while enabling secure cross-department collaboration. 

<br>

---

<h2>Technical Skills Applied</h2>

| *Security Domain* | *Commands/Concepts* | *Application* | 
| :--- | :--- | :--- | 
| *User & Group Administration* | `useradd` & `groupadd` | Created users and organized departmental access groups | 
| *Ownership Management* | `chown` & `chgrp` | Assigned proper ownership of files and directories | 
| *Permission Management* | `chmod` | Enforced least privilege access controls | 
| *ACL Configuration* | `setfacl` | Granted granular access beyond standard Linux permissions | 
| *ACL Auditing* | `getfacl` | Verified and reviewed ACL assignments | 
| *Access Control Design* | Linux RBAC Concepts | Implemented RBAC based on department  | 

<br>

---

<h2>Access Control Implementation</h2>

The environment was designed using the principle of least privilege while allowing controlled business collaboration between departments. 

&emsp;☑ Segregated *IT*, *Marketing*, and *Finance* access <br>
&emsp;☑ Restricted access to sensitive departmental data <br>
&emsp;☑ Enabled controlled cross-department collaboration <br>
&emsp;☑ Granted temporary elevated permissions only when operationally required <br>

<br>

<details markdown="block"><summary>View ACL Implementation Examples</summary>

1. Assign team members to their groups:
``` bash 
   sudo useradd -G Marketing Rhonda && sudo useradd -G Marketing Amanda && sudo useradd -G Marketing Gabe
```

2. Grant *Finance* group read (r) and execute (x) access to *Marketing* group:
``` bash
   setfacl -m g:Finance:r-x Marketing
```

3. Grant a specific user full access:
``` bash 
   setfacl -m u:Amanda:rwx Marketing
```

4. Grant *IT* leadership cross-department visibility:
``` bash
   sudo setfacl -m u:Deena:r-x Marketing
   sudo setfacl -m u:Deena:r-x Finance
```

5. View ACL permissions for the *Marketing* team:
``` bash 
   getfacl Marketing
```

6. Allow *Finance* access to only read and execute for the *Marketing* directory:
``` bash
   sudo setfacl -m g:Finance:r-x Marketing
```

</details>

<br>

---

<h2>Bash Automation</h2>

To improve consistency and reduce the risk of manual configuration errors, I created a Bash script that automated:   

&emsp;☑ Creating users and groups <br>
&emsp;☑ Assigning group membership <br>
&emsp;☑ Creating user directories <br>
&emsp;☑ Setting ownership and permissions <br>
&emsp;☑ Enforcing access controls through Linux permissions <br>

<br>

<details markdown="block"><summary>View Command Examples</summary>
   
1. Create group if it doesn't exist:
``` bash 
   groupadd $GROUPNAME
```

2. Create user and assign group:
``` bash
   useradd -m -d $USERDIR -s /bin/bash -g $GROUPNAME $USERNAME
```

3. Create directory:
``` bash 
   mkdir $USERDIR/myfolder
```

4. Set permissions and ownership:
``` bash 
   chmod 700 $USERDIR/myfolder

   chown $USERNAME:$GROUPNAME $USERDIR/myfolder
```

</details>

<br>

---

<h2>Results</h2>

* Implemented department-based access controls. <br>
* Applied least-privilege permissions. <br>
* Enabled secure cross-department collaboration. <br>
* Configured user-specific ACL exceptions. <br>
* Automated repetitive user provisioning tasks. <br> 

<br>

---

<h2>Key Takeaway</h2>

This project demonstrated how Linux ACLs extend traditional permission models to support real-world business requirements. Using ACLs, group management, and automation, I implemented a secure access framework that balanced least privilege with operational collaboration. 
