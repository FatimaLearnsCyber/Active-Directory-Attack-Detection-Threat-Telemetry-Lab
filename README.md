# Active-Directory-Attack-Detection-Threat-Telemetry-Lab
*Windows Server 2022 | Active Directory | Splunk SIEM | Sysmon | Kali Linux | Atomic Red Team*


## Project Overview
*This project demonstrates the design and implementation of an enterprise-style Active Directory security lab focused on attack simulation and detection using Splunk SIEM.*

---

![Architecture Diagram](architecture-diagram.png)

---

The lab replicates a real-world SOC environment where:
- A domain controller manages identity and authentication
- Endpoints generate telemetry using Sysmon
- Logs are forwarded to a centralized SIEM (Splunk)
- Attacks are simulated using Kali Linux and Atomic Red Team
- Security events are detected and investigated using log analysis

The objective was to demonstrate how Security Operations Centers (SOCs) monitor authentication activity, detect suspicious behavior, and investigate attacks using log data and threat intelligence frameworks.

## Value & Impact of the Lab


---

## Lab Architecture
### Environment Components

| Component | Role |
|----------|------|
| Windows Server 2022 | Active Directory Domain Controller |
| Windows 10 | Domain-joined endpoint (target machine) |
| Ubuntu Server | Splunk SIEM |
| Kali Linux | Attacker machine |

### Network Configuration

- Network: 192.168.10.0/24
- Domain: fatimacyber.local
- AD Server: 192.168.10.7
- Splunk Server: 192.168.10.10
- Attacker (Kali): 192.168.10.250

---

## Key Components Explained

#### Active Directory (AD):
Centralized identity management system used for authentication and authorization.

#### Splunk (SIEM):
Log analysis platform used to collect, search, and visualize security events.

#### Splunk Universal Forwarder:
Lightweight agent that sends logs from endpoints to the Splunk server.

#### Sysmon:
Advanced Windows logging tool that records process creation, network connections, and system activity.

#### Kali Linux:
Attacker machine used to simulate brute-force attacks.

#### Hydra:
Password-cracking tool used to perform RDP brute-force attacks.

#### Atomic Red Team:
Tool used to simulate real-world attacker techniques mapped to MITRE ATT&CK.

---

## Phase 1 — Network & Splunk Setup
*Configured all virtual machines under a single NAT network (AD-Project) for communication.*

Installed Splunk on Ubuntu:

```bash
wget -O splunk-installer.deb "https://download.splunk.com/..."
sudo dpkg -i splunk-installer.deb
sudo /opt/splunk/bin/splunk start --accept-license
```
Purpose: 
* Central platform for log ingestion, indexing, and analysis. 

### Configure receiving port:

Settings → Forwarding & Receiving → Add Port **9997**

Purpose:
* Allows Splunk to receive logs from forwarders.


### Create Index
Index Name: *"endpoint"*

Purpose:
* Stores endpoint security logs separately for easier analysis.

---

## Phase 2 - Endpoint Telemetry (Windows + AD)

### Install Splunk Universal Forwarder
- Installed on:
  - Windows 10
  - Active Directory Server

Purpose:
* Forwards logs from endpoints to Splunk server.

### Configure Forwarder (inputs.conf)
Destination: ```192.168.10.10:9997```

Edited inputs.conf:
```bash
[WinEventLog://Security]
[WinEventLog://System]
[WinEventLog://Application]
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
```

Purpose:
* Collect system, security, and Sysmon logs.

Restarted service:
```bash
net stop splunkforwarder
net start splunkforwarder
```

---

## Phase 3 — Sysmon Installation
Installed Sysmon with configuration:
```bash
sysmon.exe -i sysmonconfig.xml
```
Purpose:
* Capture detailed endpoint activity including process execution and network connections.

---

## Phase 4 — Active Directory Setup (Windows Server 2022)
*Installed AD Domain Services and promoted server to Domain Controller.*
* Server Manager → Add Roles → Active Directory Domain Services

Purpose:
* Enables domain-based authentication and identity management.

#### Promote to Domain Controller
* Created new domain:

Created new domain:
```bash
fatimacyber.local
```

#### Created Organizational Units & Users:

- OU: IT → User: ```Roshni Anwar```
- OU: HR → User: ```Faaran Sikandar```

Purpose:
* Simulates real organizational structure.

---

## Phase 5 — Domain Join (Windows 10)

System Settings → About → Domain 

#### Initial Error:
- Domain controller could not be contacted

#### Resolution:

Changed DNS to AD server IP:
```bash
192.168.10.7
```
Successfully joined domain and logged in with domain user.

---

