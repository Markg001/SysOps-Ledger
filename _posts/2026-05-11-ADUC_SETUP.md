---
title: " Active Directory Users and Computers (ADUC) Setup Guide"
date: 2026-05-11
categories: [active directory Users and Computers]
tags: [active directory, aduc, windows server administration]
---

# Active Directory Users and Computers (ADUC) Setup Guide

Active Directory Users and Computers (ADUC) is a Microsoft Management Console (MMC) snap-in that allows administrators to manage Active Directory objects such as users, groups, computers, and organizational units (OUs). This guide will walk you through the steps to set up ADUC on a Windows Server.

This post walks you through the process of:

1. Creating a new user and setting credentials  
2. Creating security groups  
3. Adding users to groups  
4. Understanding group scopes and security types  

---

## Creating a New User in Active Directory

**Description:**  
User accounts represent individual people or systems that need access to your domain’s resources. Every user who logs into the network must have a properly configured account in Active Directory.

### Steps:

1. Open the Active Directory Users and Computers snap-in by typing `dsa.msc` in the Run dialog (`Win + R`) and pressing Enter.  
(or) navigate through the Start menu:


Start > Administrative Tools > Active Directory Users and Computers


<img src="/assets/img/posts/aduc-setup/aduc_setup_1.png" alt="ADUC Main Window" style="width:100%; max-width:700px; border-radius:10px;">

2. In the left pane, expand your domain.

<img src="/assets/img/posts/aduc-setup/aduc_setup_2.png" alt="Expand Domain" style="width:100%; max-width:700px; border-radius:10px;">

3. Once the domain is expanded, you will see several built-in folders such as Users, Computers, and Domain Controllers.

<img src="/assets/img/posts/aduc-setup/aduc_setup_3.png" alt="Built-in Folders" style="width:100%; max-width:700px; border-radius:10px;">

4. Click on the Users folder.

5. In the right pane, right-click and select **New > User**.

<img src="/assets/img/posts/aduc-setup/aduc_setup_4.png" alt="Create New User" style="width:100%; max-width:700px; border-radius:10px;">

6. Fill in the user’s first name, last name, and user logon name (username). Click **Next**.

<img src="/assets/img/posts/aduc-setup/aduc_setup_5.png" alt="User Information" style="width:100%; max-width:700px; border-radius:10px;">

7. Set a password for the user and configure password options (e.g., user must change password at next logon). Click **Next**.

<img src="/assets/img/posts/aduc-setup/aduc_setup_6.png" alt="Set User Password" style="width:100%; max-width:700px; border-radius:10px;">

8. Review the user information and click **Finish** to create the user account. The new user will now appear in the Users folder.

---

## Creating a Group in Active Directory

**Description:**  
Groups are used to manage permissions and access rights collectively. Instead of assigning permissions to each user individually, you assign them to a group, making administration simpler and more secure.

### Steps:

1. In ADUC, right-click the Users folder → **New > Group**.

<img src="/assets/img/posts/aduc-setup/aduc_setup_7.png" alt="Create New Group" style="width:100%; max-width:700px; border-radius:10px;">

2. Enter a name for the group and select the appropriate group scope (Global, Domain Local, or Universal) and group type (Security or Distribution).

<img src="/assets/img/posts/aduc-setup/aduc_setup_8.png" alt="Group Properties" style="width:100%; max-width:700px; border-radius:10px;">

3. The **Group Scope** defines how widely the group can be used:

- **Domain Local** — Access limited to one domain.  
- **Global** — Access across trusted domains in the forest.  
- **Universal** — Access across the entire forest (used in multi-domain setups).  

Choose the **Group Type**:

- **Security** — Used to assign permissions to shared resources.  
- **Distribution** — Used for email distribution lists only.  

Click **OK** to create the group.

---

## Adding Users to a Group

**Description:**  
Adding users to groups simplifies permission management. Users automatically inherit all permissions assigned to the group.

### Steps:

1. In ADUC, navigate to the group you want to modify (e.g., the group you just created).

2. Right-click the group and select **Properties**.

<img src="/assets/img/posts/aduc-setup/aduc_setup_9.png" alt="Group Properties" style="width:100%; max-width:700px; border-radius:10px;">

3. Click the **Members** tab.

4. Click **Add** and select the users you want to add to the group.

5. Click **OK** to save the changes.

<img src="/assets/img/posts/aduc-setup/aduc_setup_10.png" alt="Add Users to Group" style="width:100%; max-width:700px; border-radius:10px;">

---

## Understanding Group Scopes and Security Types

**Description:**  
Group scopes and types define how and where groups can be used within your network. Selecting the right scope ensures proper access control and prevents permission issues.

1. **Domain Local Groups:** Used to assign permissions to resources within one domain.  
2. **Global Groups:** Typically used to group users who share similar roles within the same domain.  
3. **Universal Groups:** Used across multiple domains for assigning rights to users or groups in a forest.  
4. **Security Groups:** Used to assign permissions to files, folders, printers, and other resources.  
5. **Distribution Groups:** Used only for sending email messages; they can’t be assigned permissions.  

---

### Why use Groups?

Groups are the foundation of efficient security and resource management in Active Directory. By using groups, administrators can apply rules, permissions, and policies to multiple users at once.

### Benefits:

- Simplifies permission assignment  
- Enhances security through role-based access control  
- Allows group-based policy deployment (via GPOs)  
- Reduces administrative workload  

---

## Conclusion

Creating users and groups in Active Directory is the first and most important step toward managing a secure and organized network.

By learning how to properly create users, assign them to groups, and apply the correct scope and security settings, administrators can streamline daily management and enforce strong security practices.

