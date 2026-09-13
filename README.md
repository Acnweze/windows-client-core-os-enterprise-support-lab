# Windows Client Core OS Enterprise Support Lab

A hands-on Azure lab designed around real-world Windows Client Enterprise Support responsibilities. The project covers Windows 11 administration, Active Directory, DNS, DHCP investigation, Group Policy, Windows Firewall, Remote Desktop, performance troubleshooting, and PowerShell automation.

## Project Objective

Build and troubleshoot a Windows Client environment that demonstrates practical skills required for enterprise support and infrastructure roles, including:

- Windows Client deployment and administration
- Active Directory and domain management
- DNS configuration and troubleshooting
- DHCP configuration and investigation
- Group Policy management
- Windows Firewall administration
- Remote Desktop Services (RDP) troubleshooting
- Windows performance analysis
- PowerShell diagnostics and automation
- Security and access management

## Azure Architecture

```text
Azure
└── RG-WindowsClient-Core
    └── VNet-WindowsCore (10.50.0.0/16)
        └── Servers-Clients (10.50.1.0/24)
            ├── NS-DC01   Windows Server 2025
            └── NS-W11-01 Windows 11 Enterprise N
```

**Region:** UK South  
**Domain:** `northstar.local`  
**Domain Controller:** `NS-DC01` — `10.50.1.4`  
**Windows 11 Client:** `NS-W11-01` — `10.50.1.5`

## Technologies

- Microsoft Azure
- Windows Server 2025
- Windows 11 Enterprise N
- Active Directory Domain Services (AD DS)
- DNS Server
- DHCP Server
- Group Policy
- Windows Firewall
- Remote Desktop Protocol (RDP)
- PowerShell
- Performance Monitor / `Get-Counter`

## Lab Implementation

### 1. Active Directory and DNS

Configured `NS-DC01` as the domain controller for `northstar.local` and verified AD DS, DNS, forest configuration, Global Catalog, and FSMO roles.

The Windows 11 client was configured to use the domain controller for DNS and successfully joined the `northstar.local` domain.

### 2. DHCP Investigation

Installed and authorized the Windows DHCP Server role and configured a test scope:

- Scope: `10.50.1.0/24`
- Address range: `10.50.1.100 - 10.50.1.200`
- DNS server: `10.50.1.4`
- DNS domain: `northstar.local`
- Router: `10.50.1.1`

During troubleshooting, the DHCP server showed **zero client requests and zero leases**. Further investigation demonstrated that Azure VMs on this subnet were receiving their IP configuration from Azure platform DHCP (`168.63.129.16`), while DNS was successfully delegated to `NS-DC01`.

This was documented as a troubleshooting finding rather than incorrectly claiming that the Windows DHCP server was serving the Azure subnet.

### 3. Active Directory Organizational Structure

Created an enterprise-style OU structure:

```text
NorthStar-Users
├── IT
├── HR
└── Finance

NorthStar-Computers
├── Windows-11
└── Servers

NorthStar-Admins
└── Domain Controllers
```

Created the test IT user `Sarah Johnson` and moved `NS-W11-01` into the dedicated Windows 11 OU.

### 4. Group Policy

Created and linked:

`NorthStar-Windows11-Baseline`

The GPO was linked to:

`OU=Windows-11,OU=NorthStar-Computers,DC=northstar,DC=local`

Policy application was verified from the Windows 11 client using `gpupdate` and `gpresult`.

A Windows Firewall setting was also applied through Group Policy and verified on the client.

### 5. Windows Firewall and RDP

Verified the Windows Firewall profiles and configured the domain firewall policy through Group Policy.

Remote Desktop troubleshooting included:

- `TermService` status verification
- RDP registry configuration verification
- Remote Desktop firewall rule verification
- TCP port `3389` testing
- RDP listener verification with `Get-NetTCPConnection`

Connectivity to `10.50.1.5:3389` was successfully confirmed from the domain controller.

### 6. Windows Performance Troubleshooting

Collected baseline performance data using PowerShell and Windows performance counters.

#### CPU

System-wide CPU usage remained approximately **0–1%** during the baseline collection.

#### Memory

The Windows 11 VM had approximately:

- Total memory: **7.99 GB**
- Used memory: **2.9 GB**
- Free memory: **5.09 GB**

No significant memory pressure was observed.

#### Disk

Disk performance remained very low during the baseline:

- Disk Time: approximately **0.01–0.07%**
- Average Disk Queue Length: approximately **0.0002–0.0015**
- Disk Transfers/sec: approximately **1–9.5**

The results indicated no active CPU, memory, or disk bottleneck during testing.

### 7. PowerShell Health Check Automation

Created a reusable PowerShell health-check script:

`C:\WindowsClientHealthCheck.ps1`

The script reports:

- Computer name and current user
- Windows version and architecture
- Last boot time
- CPU usage
- Total, used, and free memory
- Local disk capacity and free space
- RDP and Windows Defender service status
- RDP configuration status

Example output confirmed:

```text
OS: Microsoft Windows 11 Enterprise N
Architecture: 64-bit
CPU Usage: 0.01%
Total Memory: 7.99 GB
Used Memory: 2.92 GB
Free Memory: 5.07 GB
TermService: Running
WinDefend: Running
RDP: Enabled
```

## Troubleshooting Approach

This lab focused on evidence-based troubleshooting rather than assuming that a configured service was working.

Examples include:

- Investigating why the Windows DHCP server received no Azure VM DHCP requests
- Correcting DNS configuration so the Windows client could resolve the AD domain
- Troubleshooting domain join authentication
- Verifying that Group Policy was applied to the correct computer OU
- Validating RDP at the service, firewall, listener, and TCP connectivity levels
- Correlating process-level resource usage with system-wide performance counters
- Using PowerShell to automate repeatable health checks

## Skills Demonstrated

`Azure` `Windows Server` `Windows 11` `Active Directory` `AD DS` `DNS` `DHCP` `Group Policy` `Windows Firewall` `RDP` `PowerShell` `Performance Troubleshooting` `Network Troubleshooting` `System Administration` `Enterprise Support`

## Screenshots

All project evidence is stored in the [`screenshots`](./screenshots) folder.

### Active Directory and DNS

- [Domain Controller Success](./screenshots/domain-controller-success.png)
- [AD DS Installed](./screenshots/ad-ds-installed.png)
- [AD DNS Verification](./screenshots/ad-dns-verification.png)
- [Windows 11 DNS Verification](./screenshots/windows11-dns-verification.png)
- [Windows 11 Domain Join](./screenshots/windows11-domain-join.png)

### DHCP

- [DHCP Role Installed](./screenshots/dhcp-role-installed.png)
- [DHCP Authorized](./screenshots/dhcp-authorized.png)
- [DHCP Scope and Options](./screenshots/dhcp-scope-options.png)

### Active Directory Structure

- [Active Directory OU Structure](./screenshots/active-directory-ou-structure.png)
- [Windows 11 Computer OU](./screenshots/windows11-computer-ou.png)
- [Active Directory IT User](./screenshots/active-directory-it-user.png)

### Group Policy and Security

- [GPO Linked to Windows 11](./screenshots/gpo-linked-to-windows11.png)
- [Windows 11 GPO Applied](./screenshots/windows11-gpo-applied.png)
- [Windows 11 Firewall GPO Applied](./screenshots/windows11-firewall-gpo-applied.png)

### RDP and Performance

- [RDP Listener Verification](./screenshots/rdp-listener-verification.png)
- [Windows 11 Disk Performance](./screenshots/windows11-disk-performance.png)
- [Windows Client Health Check](./screenshots/windows-client-health-check.png)

## Key Takeaways

This project demonstrates the ability to build a Windows Client environment in Azure, administer enterprise Windows services, investigate real infrastructure behavior, validate connectivity and policy application, analyze system performance, and automate routine health checks with PowerShell.

The emphasis throughout the lab was on **practical troubleshooting, verification, and documented evidence** rather than simply installing Windows services.
