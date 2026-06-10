---
title: "All About DNS in Active Directory"
date: 2026-06-10
categories: [cybersecurity, windows-server, active-directory]
tags: [ windows-server-2016, dnssec, powershell, network-security]
---

# All About DNS in Active Directory

## Introduction

The **Domain Name System (DNS)** is one of the most critical services in modern networks. In Microsoft Active Directory environments, DNS is not simply a name-resolution service, it is a foundational component that enables domain controllers, authentication, service discovery, replication, and numerous directory services to function properly.

This article provides a comprehensive overview of DNS in Active Directory, covering installation, configuration, security, advanced management, and PowerShell administration techniques.

---

# What is DNS?

DNS (Domain Name System) is a distributed database used to translate human-readable hostnames into IP addresses.

For example:

| Hostname                                  | IP Address   |
| ----------------------------------------- | ------------ |
| [www.contoso.com](http://www.contoso.com) | 192.168.1.10 |
| dc01.contoso.com                          | 192.168.1.5  |

Without DNS, users would need to remember numerical IP addresses instead of meaningful hostnames.

DNS functions using a hierarchical structure:

```text
Root (.)
│
├── .com
│   └── contoso.com
│
├── .org
│
└── .net
```

### DNS in Active Directory

Active Directory heavily relies on DNS for:

* Domain Controller location
* Kerberos authentication
* Service discovery
* Replication
* Group Policy processing

Without properly configured DNS, Active Directory will not function correctly.

---

# Installing DNS on Windows Server

## Using Server Manager

1. Open Server Manager
2. Select **Manage**
3. Click **Add Roles and Features**
4. Select:

   * DNS Server
5. Complete the installation wizard
6. Restart if required

### PowerShell Installation

```powershell
Install-WindowsFeature DNS -IncludeManagementTools
```

Verify installation:

```powershell
Get-WindowsFeature DNS
```

---

# The Hosts File

Before querying DNS servers, Windows checks the local **Hosts File**.

Location:

```text
C:\Windows\System32\drivers\etc\hosts
```

Example:

```text
192.168.1.10  server01
192.168.1.20  webserver
```

## Security Risks

Attackers often manipulate the hosts file to:

* Redirect users to malicious sites
* Block security updates
* Prevent antivirus communication

Example:

```text
192.168.1.50 banking-site.com
```

Users attempting to reach the legitimate banking site are redirected locally.

---

# Recursive and Iterative Queries

DNS clients and servers use recursive and iterative queries.

## Recursive Query

The DNS server must return:

* The answer
* Or an error

Example:

```text
Client → DNS Server → Internet DNS Hierarchy
```

The client expects a final answer.

---

## Iterative Query

The DNS server returns the best answer it knows.

Example:

```text
Client → Root Server
Root → .com Server

Client → .com Server
.com → contoso.com Server
```

The client performs additional lookups.

---

# DNS Resource Record Types

DNS stores information using resource records.
<img src="/assets/img/posts/all-about-dns/dns_record.png" alt="DNS Resource Record Types">

## A Record
An A record (Address Record) maps an FQDN (fully qualified domain name) to an IPv4 address.
Maps hostname to IPv4 address.

```text
server01 → 192.168.1.10
```

## AAAA Record
An AAAA record (Quad-A Record) maps an FQDN to an IPv6 address.

Maps hostname to IPv6 address.

```text
server01 → 2001:db8::1
```

## PTR Record
A PTR record (Pointer Record) maps an IP address to a hostname. its the opposite of an A or AAAA record and is used in reverse lookup zones.
Reverse lookup.

```text
192.168.1.10 → server01
```

## CNAME Record
A CNAME record (Canonical Name Record) creates an alias for another hostname. It allows multiple hostnames to point to the same IP address without creating multiple A or AAAA records. Also known as an 
Alias record.

```text
www → webserver.contoso.com
```

## MX Record
A MX record (Mail Exchanger Record) specifies the mail server responsible for accepting email messages on behalf of a domain. It is essential for email delivery and routing.
Mail routing.

```text
mail.contoso.com
```

## NS Record
A NS record (Name Server Record) specifies the authoritative name servers for a domain. It indicates which DNS servers are responsible for handling queries for that domain.
Name server information.

## SRV Record
A SRV record (Service Record) specifies the location of services within a domain. It is commonly used in Active Directory to locate domain controllers and other services.
Service location.
**Critical for Active Directory.**

Example:

```text
_ldap._tcp.dc._msdcs.contoso.com
```

Used to locate domain controllers.

## SOA Record
Start of Authority record contains zone information. 
Every DNS zone has an SOA record that defines the primary name server, responsible party, and zone settings. 
example:

```text 
@ IN SOA ns1.contoso.com. admin.contoso.com. (
    2024061001 ; Serial
    3600       ; Refresh
    1800       ; Retry
    604800     ; Expire
    86400      ; Minimum TTL
)
```
---

# Creating a Forward Lookup Zone

Example domain:

```text
mytestzone.local
```

## Steps

1. Open DNS Manager
2. Right-click Forward Lookup Zones
3. New Zone
4. Primary Zone
5. Enter:
<img src="/assets/img/posts/all-about-dns/new_zone_wizard.jpg" alt="New Zone Wizard" class="shadow">
```text
mytestzone.local
```

6. Finish Wizard

PowerShell:

```powershell
Add-DnsServerPrimaryZone `
-Name "mytestzone.local" `
-ZoneFile "mytestzone.local.dns"
```
<img src="/assets/img/posts/all-about-dns/zone_creation.jpg" alt="PowerShell Zone Creation" class="shadow">
---

# Creating DNS Resource Records
**More resources on the same click the below link:**

[All about DNS Console](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc958958(v=technet.10)?redirectedfrom=MSDN)

---

[All about DNS Console](https://learn.microsoft.com/en-us/windows-server/networking/technologies/ipam/dnsresource-record-management)

## A Record
An A record (Address Record) maps an FQDN (fully qualified domain name) to an IPv4 address. its the most common type of DNS record and is used to resolve hostnames to IP addresses.
How to create an A record:
1. Open DNS Manager
2. Expand the zone
3. Right-click the zone
4. Select **New Host (A or AAAA)**
5. Enter the hostname and IP address
6. Click **Add Host**
7. Finish

```powershell
Add-DnsServerResourceRecordA `
-Name "Server01" `
-ZoneName "mytestzone.local" `
-IPv4Address "192.168.1.10"
```

---

## CNAME
A CNAME record (Canonical Name Record) creates an alias for another hostname. It allows multiple hostnames to point to the same IP address without creating multiple A or AAAA records. Also known as an Alias record.
How to create a CNAME record:
1. Open DNS Manager
2. Expand the zone
3. Right-click the zone
4. Select **New Alias (CNAME)**
5. Enter the alias name and the fully qualified domain name (FQDN) of the target host
6. Click **OK**
7. Finish

```powershell
Add-DnsServerResourceRecordCName `
-Name "www" `
-HostNameAlias "server01.mytestzone.local" `
-ZoneName "mytestzone.local"
```

---

# DNS Zones

A DNS Zone is a database portion managed by a DNS server.

Types:

* Primary Zone
* Secondary Zone
* Stub Zone
* Active Directory Integrated Zone

---

# Creating Forward and Reverse Lookup Zones

## Forward Lookup Zone

Hostname → IP

```text
server01 → 192.168.1.10
```

---

## Reverse Lookup Zone

IP → Hostname

```text
192.168.1.10 → server01
```

PowerShell:

```powershell
Add-DnsServerPrimaryZone `
-NetworkID "192.168.1.0/24" `
-ReplicationScope Forest
```

---

# Creating a Secondary Zone

Secondary zones provide redundancy.

Benefits:

* Load balancing
* Fault tolerance
* Reduced WAN traffic

PowerShell:

```powershell
Add-DnsServerSecondaryZone `
-Name "contoso.com" `
-ZoneFile "contoso.com.dns" `
-MasterServers 192.168.1.10
```

---

# Stub Zone Creation

Stub zones contain:

* SOA records
* NS records
* Glue A records

PowerShell:

```powershell
Add-DnsServerStubZone `
-Name "partner.local" `
-MasterServers 192.168.2.10 `
-ZoneFile "partner.local.dns"
```

Benefits:

* Reduced administration
* Efficient name resolution

---

# Active Directory Zone Replication

AD-integrated zones replicate through Active Directory.

Replication scopes:

## Forest

```text
All DNS servers in the forest
```

---

## Domain

```text
All DNS servers in the domain
```

---

## Custom Application Partition

Specific DNS servers only.

PowerShell:

```powershell
Get-DnsServerZone
```

---

# Implementing DNS Forwarding

Forwarders send unresolved requests to another DNS server.

Example:

```text
Internal DNS → Google DNS
```

PowerShell:

```powershell
Add-DnsServerForwarder `
-IPAddress 8.8.8.8,1.1.1.1
```

---

# Implementing Conditional DNS Forwarding

Conditional forwarders forward requests for specific domains.

Example:

```text
partner.local
```

Forward only to:

```text
192.168.10.10
```

PowerShell:

```powershell
Add-DnsServerConditionalForwarderZone `
-Name "partner.local" `
-MasterServers 192.168.10.10
```

---

# Domain Name System Delegation

Delegation allows a parent zone to assign authority.

Example:

```text
contoso.com
│
└── branch.contoso.com
```

Benefits:

* Distributed administration
* Scalability

---

# Windows Server 2016 DNS Zone Delegation

Steps:

1. Open DNS Manager
2. Parent Zone
3. New Delegation
4. Specify child domain
5. Add authoritative name server

PowerShell:

```powershell
Add-DnsServerZoneDelegation
```

---

# DNS Security Techniques

Security measures include:

* DNSSEC
* Cache Locking
* Socket Pools
* Response Rate Limiting
* Secure Dynamic Updates
* Split Brain DNS

---

# Configuring DNS Cache Locking

Cache locking prevents cache poisoning.

View setting:

```powershell
Get-DnsServerCache
```

Configure:

```powershell
Set-DnsServerCache `
-LockingPercent 100
```

---

# Configuring DNS Socket Pools

Socket pools randomize source ports.

View:

```powershell
Get-DnsServerSetting
```

Configure:

```powershell
Set-DnsServerSetting `
-SocketPoolSize 10000
```

---

# Configuring Response Rate Limiting (RRL)

Protects against DNS amplification attacks.

```powershell
Add-DnsServerResponseRateLimiting `
-ResponsesPerSec 5
```

Benefits:

* DDoS mitigation
* Reduced abuse

---

# Overview of Advanced DNS Topics

Advanced DNS capabilities include:

* DNSSEC
* Split Brain DNS
* DNS Policies
* Traffic Management
* Selective Recursion
* Geo-location Responses

---

# Enabling Round Robin and Netmask Ordering

## Round Robin

Distributes requests among multiple hosts.

```powershell
Set-DnsServerGlobalNameZone `
-Enable $true
```

---

## Netmask Ordering

Returns addresses closest to clients.

```powershell
Set-DnsServerSetting `
-LocalNetPriority $true
```

---

# Configuring Recursion

Disable recursion:

```powershell
Set-DnsServerRecursion `
-Enable $false
```

Verify:

```powershell
Get-DnsServerRecursion
```

---

# IPv4 and IPv6 Root Hints

Root hints help locate internet DNS servers.

View:

```powershell
Get-DnsServerRootHint
```

IPv4 Example:

```text
198.41.0.4
```

IPv6 Example:

```text
2001:503:ba3e::2:30
```

---

# Windows DNS Security Overview

Threats include:

* DNS spoofing
* Cache poisoning
* Reflection attacks
* Tunneling attacks
* Unauthorized zone transfers

Mitigations:

* DNSSEC
* RRL
* Secure updates
* Firewall controls
* DNS policies

---

# Symmetric vs Asymmetric Encryption

## Symmetric Encryption

Uses one key.

Examples:

* AES
* DES

Advantages:

* Fast

Disadvantages:

* Key distribution challenges

---

## Asymmetric Encryption

Uses two keys.

Examples:

* RSA
* ECC

Advantages:

* Secure key exchange

Disadvantages:

* Slower

DNSSEC primarily uses asymmetric cryptography.

---

# Installing DNSSEC on Windows Server 2016

Enable DNSSEC signing:

```powershell
Invoke-DnsServerZoneSign `
-ZoneName "contoso.com"
```

Create signing keys:

```powershell
Add-DnsServerSigningKey
```

Verify:

```powershell
Get-DnsServerZone
```

---

# DNSSEC Client Installation

Validate DNSSEC:

```powershell
Resolve-DnsName `
-Name microsoft.com `
-DnssecOk
```

Group Policy may be used to enforce DNSSEC validation.

---

# DNS Policies Background Information

DNS Policies introduced in Windows Server 2016 provide intelligent DNS responses.

Use cases:

* Geo-location
* Split Brain DNS
* Traffic balancing
* Security filtering

---

# Configuring DNS Filtering

Block malicious domains.

Example:

```powershell
Add-DnsServerQueryResolutionPolicy
```

Filter:

```text
maliciousdomain.com
```

Benefits:

* Malware prevention
* Data loss prevention

---

# Configuring Split Brain DNS in Active Directory

Split Brain DNS provides different answers internally and externally.

Example:

## Internal

```text
portal.contoso.com
→ 10.1.1.10
```

## External

```text
portal.contoso.com
→ 198.51.100.10
```

Benefits:

* Improved security
* Internal resource access

---

# Configuring DNS Selective Recursion Policy

Example:

Allow recursion:

```text
Internal Clients
```

Block recursion:

```text
External Clients
```

PowerShell:

```powershell
Add-DnsServerQueryResolutionPolicy
```

---

# Configuring a Traffic Management Policy

Direct traffic based on:

* Location
* Subnet
* Time of day

Example:

```powershell
Add-DnsServerZoneScope
```

Create policy:

```powershell
Add-DnsServerQueryResolutionPolicy
```

Use cases:

* Disaster recovery
* Load balancing
* Geo-distribution

---

# Essential DNS PowerShell Commands

## View DNS Server Settings

```powershell
Get-DnsServer
```

---

## View Zones

```powershell
Get-DnsServerZone
```

---

## View Resource Records

```powershell
Get-DnsServerResourceRecord `
-ZoneName "contoso.com"
```

---

## Create A Record

```powershell
Add-DnsServerResourceRecordA `
-Name "Web01" `
-ZoneName "contoso.com" `
-AllowUpdateAny `
-IPv4Address "192.168.1.20"
```

---

## Remove Record

```powershell
Remove-DnsServerResourceRecord
```

---

## Flush Cache

```powershell
Clear-DnsServerCache
```

---

## Restart DNS Service

```powershell
Restart-Service DNS
```

---

# Best Practices for Active Directory DNS

1. Use AD-Integrated Zones
2. Enable Secure Dynamic Updates
3. Implement DNSSEC
4. Configure Conditional Forwarders
5. Restrict Zone Transfers
6. Monitor DNS Logs
7. Use Response Rate Limiting
8. Implement Split Brain DNS
9. Regularly Audit DNS Records
10. Backup DNS Zones

---

# Conclusion

DNS is the backbone of Active Directory and enterprise networking. Properly configured DNS enables authentication, service discovery, replication, and secure communication across an organization's infrastructure. Windows Server 2016 provides powerful DNS capabilities including DNSSEC, DNS Policies, Split Brain DNS, Traffic Management, Conditional Forwarding, and advanced security controls. Understanding these features and implementing them correctly helps administrators build resilient, scalable, and secure Active Directory environments.
