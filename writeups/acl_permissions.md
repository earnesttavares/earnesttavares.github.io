---
title: ACLs & Permissions 
parent: Writeups
nav_order: 2
---

<h1 style="color:#33aaff;">Linux ACLs & Permissions</h1> 

<h2 style="color:#D4A017;">Overview</h2>

Linux Access Control Lists (ACLs) provide granular control over file and directory permissions, allowing administrators to grant access to specific users and groups beyond standard permission settings. ACLs help organizations enforce the principle of least privilege by ensuring users only have access to the resources necessary for their roles. In a simulated enterprise environment, I used ACLs, groups, and directory permissions to implement secure access controls across multiple departments while supporting business collaboration. 

---

<h2 style="color:#D4A017;">Skills Demonstrated</h2>

* User and group management with `useradd` and `groupadd`.
* File and directory ownership management using `chown` and `chgrp`.
* Permission management with `chmod`.
* Access Control List (ACL) configuration using `setfacl`.
* ACL verification using `getfacl`.
* Implementation of role-based access control principles.

---

<h2 style="color:#D4A017;">Security Controls Implemented</h2>

Separate department groups for IT, Marketing, and Finance were created. Users were assigned to their appropriate department groups, and department directories were configured with specific ownership and permissions to protect sensitive information.

<br>

To support collaboration while maintaining security, I used ACLs to grant selective access: 

1. Finance received read and execute access to Marketing resources.
2. IT leadership was granted cross-department visibility.
3. Specific users received elevated privileges when required for business operations.
4. Marketing personnel were provided access to designated non-sensitive IT data without write permissions.

--- 

<h2 style="color:#D4A017;">Automation in Access Controls</h2>

I created a Bash script that automates the process of: 

* Creating users and groups
* Assigning group membership
* Creating user directories
* Setting ownership and permissions
* Enforcing access controls through Linux permissions

<br>

This exercise demonstrated how routine administrative tasks can be automated to improve consistency, increase efficiency, and reduce the risk of manual configuration errors. 

1. Create group if it doesn't exist: <br>
   `groupadd $GROUPNAME`

2. Create user and assign group: <br>
   `useradd -m -d $USERDIR -s /bin/bash -g $GROUPNAME $USERNAME`

3. Create directory: <br>
   `mkdir $USERDIR/myfolder`

4. Set permissions and ownership: <br>
   `chmod 700 $USERDIR/myfolder`
   `chown $USERNAME:$GROUPNAME $USERDIR/myfolder`

<br>

---

<h2 style="color:#D4A017;">Example Commands</h2>

These commands demonstrate how ACLs can be used to implement the principle of least privilege while supporting operational collaboration: 

1. Assign team members to their groups: <br>
   `sudo useradd -G Marketing Rhonda && sudo useradd -G Marketing Amanda && sudo useradd -G Marketing Gabe`

2. Grant Finance group read (r) and execute (x) access to Marketing group: <br>
   `setfacl -m g:Finance:r-x Marketing`

3. Grant a specific user full access: <br>
   `setfacl -m u:Amanda:rwx Marketing`

4. View ACL permissions for the Marketing team: <br>
   `getfacl Marketing`

<br>

---

<h2 style="color:#D4A017;">Key Takeaway</h2>

Traditional Linux permissions provide control through owner, group, and others. ACLs extend this model by allowing administrators to assign unique permissions to specific users and groups, enabling more granular access control. This flexibility is especially valuable in enterprise environments where users often require access that doesn't fit standard Linux permission structures. 
