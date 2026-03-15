# Azure Cloud Honeynet & Microsoft Sentinel SIEM Implementation
![Cloud Honeynet / SOC](https://i.imgur.com/ZWxe03e.jpg)

## Technologies Used
- Microsoft Azure
- Microsoft Sentinel (SIEM)
- Log Analytics Workspace
- Kusto Query Language (KQL)
- Windows Event Logs
- Linux Syslog
- Network Security Groups (NSG)
- Cloud Security Hardening

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

## Attack Maps Before Hardening / Security Controls
<img width="1280" alt="nsg allowed in" src="https://github.com/samuelkturner/azure-net/assets/160993211/3167c22f-f52b-437d-b667-c6ad34345253">

<img width="1280" alt="linux ssh auth fail" src="https://github.com/samuelkturner/azure-net/assets/160993211/8d839696-2657-442c-9aa5-0efb2e4a8f9a">

<img width="1280" alt="windows rdp smb authentication fail" src="https://github.com/samuelkturner/azure-net/assets/160993211/ece37568-4e19-4503-8f2f-fbd998c41312">


## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-03-15 17:04:29
Stop Time 2023-03-16 17:04:29

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 36380
| Syslog                   | 19867
| SecurityAlert            | 9
| SecurityIncident         | 212
| AzureNetworkAnalytics_CL | 3078

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 17337
| Syslog                   | 1
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Security Outcomes
- Security incidents reduced from 212 to 0 after hardening
- Malicious network flows reduced from 3078 to 0
- Alerts eliminated after access restrictions implemented
- Demonstrated effectiveness of least-exposure network architecture

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.

## SOC Workflow Demonstrated
- Log ingestion and normalization
- Detection rule triggering
- Alert generation
- Incident creation in Sentinel
- Investigation using dashboards
- Security control validation

# Cloud-Based Active Directory & User Management Lab (Azure)

![Active Directory Lab](https://i.imgur.com/placeholder.jpg)

## Technologies Used

* Microsoft Azure (Virtual Machines, Virtual Networks, NSG)
* Windows Server 2025 Datacenter
* Active Directory Domain Services (AD DS)
* DNS Server
* Remote Desktop Protocol (RDP)
* Azure VNet Peering
* Windows Event Viewer (Security Logs)
* PowerShell & Command Prompt

---

## Introduction

In this project, I built a fully functional cloud-based Active Directory environment in Microsoft Azure, simulating a real-world enterprise network. I deployed a Windows Server 2025 Domain Controller (DC01) and a client machine (CLIENT01), configured a domain (corp.local), created and managed domain user accounts, and monitored authentication events using Windows Event Viewer — skills directly relevant to IT administration, help desk, and SOC analyst roles.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     Microsoft Azure                         │
│                                                             │
│  ┌──────────────────────┐     VNet Peering    ┌──────────────────────┐  │
│  │   vnet-westus-2      │◄───────────────────►│    AD-VNet           │  │
│  │   172.18.0.0/24      │                     │    10.0.0.0/24       │  │
│  │                      │                     │                      │  │
│  │  ┌────────────────┐  │                     │  ┌────────────────┐  │  │
│  │  │     DC01       │  │                     │  │   CLIENT01     │  │  │
│  │  │ Windows Server │  │                     │  │ Windows Server │  │  │
│  │  │    2025        │  │                     │  │    2025        │  │  │
│  │  │ 172.18.0.4     │  │                     │  │ 10.0.0.4       │  │  │
│  │  │                │  │                     │  │                │  │  │
│  │  │ • AD DS        │  │                     │  │ • Domain Member│  │  │
│  │  │ • DNS Server   │  │                     │  │ • RDP Enabled  │  │  │
│  │  │ • corp.local   │  │                     │  │                │  │  │
│  │  └────────────────┘  │                     │  └────────────────┘  │  │
│  └──────────────────────┘                     └──────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

---

## Project Steps

### Step 1: Deploy Virtual Machines in Azure
- Created two Windows Server 2025 VMs: **DC01** (Domain Controller) and **CLIENT01** (Client Machine)
- Configured Network Security Groups (NSGs) to allow RDP access on port 3389
- Assigned public and private IP addresses to both VMs

### Step 2: Configure Active Directory on DC01
- Installed **Active Directory Domain Services (AD DS)** role on DC01
- Promoted DC01 to a Domain Controller for the domain **corp.local**
- Verified DNS zones were automatically created in DNS Manager
- Confirmed AD DS health using Server Manager

### Step 3: Configure Networking & Join Client to Domain
- Identified VNet mismatch between DC01 (vnet-westus-2) and CLIENT01 (AD-VNet)
- Configured **Azure VNet Peering** to enable cross-network communication
- Set CLIENT01's DNS server to DC01's private IP (172.18.0.4) using `netsh`
- Verified connectivity with `ping 172.18.0.4` — 0% packet loss confirmed
- Joined CLIENT01 to the **corp.local** domain via System Properties

### Step 4: Create & Manage Domain Users
- Opened **Active Directory Users and Computers (ADUC)** on DC01
- Created Organizational Unit **TestUsers**
- Created domain user accounts: **Alice**, **Bob**, and **Charlie**
- Configured user properties and group memberships

### Step 5: Configure Remote Desktop Access
- Opened System Properties on CLIENT01 (sysdm.cpl)
- Navigated to **Remote tab → Select Users**
- Added **CORP\alice** to the **Remote Desktop Users** local group
- Verified domain user RDP access to CLIENT01

### Step 6: Test Domain User Authentication
- Logged out of CLIENT01 administrator account
- Logged in as **corp\alice** using domain credentials
- Ran `whoami` in Command Prompt — output confirmed: `corp\alice`
- Tested network access by pinging DC01 (172.18.0.4) — successful

### Step 7: Security Monitoring with Event Viewer
- Opened **Event Viewer** on DC01
- Navigated to **Windows Logs → Security**
- Used **Filter Current Log** to identify key authentication events:

| Event ID | Description | SOC Relevance |
|----------|-------------|---------------|
| 4624 | Successful logon | Confirms valid user authentication |
| 4625 | Failed logon | Detects brute-force or password guessing |
| 4648 | Logon using explicit credentials | Indicates lateral movement attempts |
| 4720 | User account created | Tracks user provisioning |

- Exported Security logs as **DC01-SecurityLogs.evtx** for offline analysis

---

## Key Challenges & Solutions

| Challenge | Root Cause | Solution |
|-----------|-----------|----------|
| CLIENT01 couldn't RDP | No Public IP assigned | Dissociated existing IP and reassigned correctly |
| Domain join failed | VMs on different VNets | Configured Azure VNet Peering between vnet-westus-2 and AD-VNet |
| DNS not resolving | DNS still pointing to wrong IP | Used `netsh interface ip set dns` to manually set DNS to 172.18.0.4 |
| Trust relationship failed | Domain trust broken after network changes | Removed computer from domain and rejoined with fresh trust |
| Cannot add domain user to RDP group via GUI | Location defaulting to local machine | Used `net localgroup` via CMD to add CORP\alice directly |

---

## Skills Demonstrated

* **Cloud Infrastructure**: Deploying and managing Azure VMs, VNets, NSGs, and Public IPs
* **Windows Server Administration**: Installing AD DS, promoting Domain Controllers, configuring DNS
* **Active Directory Management**: Creating OUs, user accounts, group memberships
* **Network Troubleshooting**: Diagnosing VNet peering issues, DNS misconfigurations, and trust relationship failures
* **Identity & Access Management**: Domain joining, RDP access control, user authentication
* **Security Monitoring**: Reviewing Windows Security Event logs, filtering by Event ID
* **PowerShell & CMD**: Using `netsh`, `ping`, `whoami`, `net localgroup`, and `ipconfig` for diagnostics

---

## Platforms & Technologies

`Microsoft Azure` `Windows Server 2025` `Active Directory Domain Services` `DNS` `RDP` `VNet Peering` `NSG` `PowerShell` `Windows Event Viewer` `Identity & Access Management`

---

## About the Author

**Samuel K. Turner**
[LinkedIn](https://linkedin.com/in/samuelkturner) | [GitHub](https://github.com/samuelkturner)