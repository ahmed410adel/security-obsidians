# What is Active Directory ?
It is a group of users and computers under the administration of a given business. The main idea behind a domain is to centralize the administration of common components of a Windows computer network in a single repository. The server that runs the Active Directory services is known as a Domain Controller (DC).
##### First Steps
1. change date
2. change IP
3. Change computer name
# Domain or Windows Server rules and services ?
#### 1. Active Directory

![[Pasted image 20260925122856.png|466]]

**Domain:** A logical group of network objects (such as users, computers, and devices) that share the same Active Directory database.
**Forest:** A collection of one or more domains that share a common schema and configuration. Each forest has a unique namespace.

Steps to add AD :
1. add role and feature
2. check active directory domain services
3. add new forest `domain.local`

	*Forest Functional Level = lowest windows server can be in the forest as a domain controller*
	*Domain Functional Level = lowest windows server can be in my child domains*
	*RODC (read only domain controller) = DC do not save passwords* 

4. type the password for backup and this is the password of the local admin
5. DNS Delegation = allow parent to write on the DNS of the child domain
6. NetBIOS = name of the domain

	*NTDS (new technology directory service) = database for AD path = C:\windows\NTDS\NTDS.dit*
	*SYSVOL = all policies and scripts executed in system and this file created by the system and its read only for all domain objects*

7. promote server to a domain controller

**Active Directory Objects:**
1. User Account
2. Computer Account
3. OU (Organizational Unit)
4. Groups
	*What is the difference between OU and Group ?*
	We apply Policy on OU while group used to grant permissions over resources. you will use groups if you want to allow some users to access a shared folder or network printer. A user can be a part of many groups, which is needed to grant access to multiple resources, user can only be a member of a single OU at a time

> all objects must be unique over the domain
#### 2. Group Policy
group policy applied over users or computers.
to update policy now you write this in run `gpudate /force` because it updates after 90 minute by default
#### 3. File Server
#### 4. DHCP
#### 5. DNS
#### 6. Print Server
#### 7. WSUS (Windows System Update Services)
#### 8. WDS (Windows Deployment Service)
#### 9. Server Core
#### 10. Hyper-V
#### 11. Remote
#### 12. Backup
#### 13. ADC
#### 14. FSMO



### How to join PC to the Domain ?
1. Authentication
2. Authorization
3. Accounting
	PC names and Objects (OU, Users, PCs) must be unique
*any member on the domain can join up to 10 PCs while the admin can join unlimited PCs*

**We can login in two ways**
1. `username@domain.local` -> domain login
2. `NETBIOS\username` -> domain login
3. `.\username` -> PC local login
#### Kerberos (authentication protocol for domain join)










![[image 2.webp]]
**Tree:** is a hierarchical structure that organizes one or more domains in a logical relationship. Each domain in a tree shares a common namespace (e.g., *sales.company.com* and *hr.company.com* under *company.com*) and a trust relationship with other domains in the same tree.

Trust Relationships:  having a trust relationship between domains allows you to authorize a user from domain *EXAMPLE UK* to access resources from domain *EXAMPLE EU*
![[image 5.webp]]

The simplest trust relationship that can be established is a **one-way trust relationship**. In a one-way trust, if `Domain A` trusts `Domain B`, this means that a user on B can be authorized to access resources on A:

**Two-way trust relationships** can also be made to allow both domains to mutually authorize users from the other. By default, joining several domains under a tree or a forest will form a two-way trust relationship.

In summary, the primary difference is that a **one-way trust** only allows one domain to access resources in another, whereas a **two-way trust** allows both domains to access each other’s resources.

**Organizational Unit (OU):** A container within a domain that can hold users, groups, computers, and other OUs. OUs are used to organize objects and apply Group Policies.

![[image 4.webp]]
**Group Policy Objects (GPOs):** Group Policies are implemented through Group Policy Objects, which are collections of settings that control the behaviour of user accounts and computer accounts in an AD environment. GPOs can be linked to different levels of the Active Directory hierarchy, including Domains The primary boundary for user and resource management and OU.

![[image 6.webp]]
**Domain Controllers:** Servers that hold the AD database and manage user logins, authentication, and directory services. 

Users can be used to represent two types of entities:
1. People: users will generally represent persons in your organization who need to access the network, like employees.
2. Services: You can also define users to be used by services like IIS or MSSQL. Every service requires a user to run, but service users are different from regular users as they have only the privileges needed to run their specific service. → (Service Users)

Machines are also considered "security principals" and are assigned an account just as any regular user. This account has limited rights within the domain itself.

The machine accounts themselves are local administrators on the assigned computer, they are generally not supposed to be accessed by anyone except the computer itself, but as with any other account, if you have the password, you can use it to log in.

Security Groups:
![[image 7.webp]]


---
# Kerberos Authentication
It is a network authentication protocol designed to provide secure identity verification in distributed computing environments. It is widely used in various operating systems, including Microsoft Windows, UNIX, and Linux, and is particularly integral to Microsoft Active Directory.
![[image 8.webp]]
all credentials are stored in the Domain Controllers. Whenever a user tries to authenticate to a service using domain credentials, the service will need to ask the Domain Controller to verify if they are the correct

**key component :**










###### NOTES :
- FQDN stand for (fully qualified domain name)
- why i make network ? to share **data, devices and connection**
- SAM file is a file that stores users and passwords in windows
- **Workgroup** is device not controlled by anyone and **Domain** is device controlled by the administrator
- domain controller is a windows server that installed on it AD DS and configured
- passwords expires after 42 days by default
- we do not delete the local admin on the device because what if we want to debug the device after it goes out of the domain by mistake we need a full administration over the device
- we also create group over all the devices to that has administration permission using group policy
- we can create group on all devices except the domain controller 