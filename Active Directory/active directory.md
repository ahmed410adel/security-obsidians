# What is Active Directory ?
It is a group of users and computers under the administration of a given business. The main idea behind a domain is to centralize the administration of common components of a Windows computer network in a single repository. The server that runs the Active Directory services is known as a Domain Controller (DC).
![[image.webp]]

**Domain:** A logical group of network objects (such as users, computers, and devices) that share the same Active Directory database.

![[image 1.webp]]

**Forest:** A collection of one or more domains that share a common schema and configuration. Each forest has a unique namespace.

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

## OUs Vs Security Group
**OUs:** include specific configurations that pertain to sets of users depending on their particular role in the enterprise, user can only be a member of a single OU at a time (configuration)

**Security Groups:** used to grant permissions over resources. you will use groups if you want to allow some users to access a shared folder or network printer. A user can be a part of many groups, which is needed to grant access to multiple resources (access control)

---
# Kerberos Authentication
It is a network authentication protocol designed to provide secure identity verification in distributed computing environments. It is widely used in various operating systems, including Microsoft Windows, UNIX, and Linux, and is particularly integral to Microsoft Active Directory.
![[image 8.webp]]
all credentials are stored in the Domain Controllers. Whenever a user tries to authenticate to a service using domain credentials, the service will need to ask the Domain Controller to verify if they are the correct

**key component :**










###### NOTES :
- FQDN stand for (fully qualified domain name)
- SAM file is a file that stores users and passwords in windows