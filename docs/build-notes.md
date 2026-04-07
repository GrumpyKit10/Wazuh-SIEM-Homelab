# Build Notes – Wazuh SIEM Homelab

## Overview
This document outlines the step-by-step build process for creating an isolated Wazuh SIEM lab environment, including infrastructure setup, network configuration, Wazuh deployment, and agent onboarding.

---

## Phase 1: Virtual Machine Setup

### Wazuh Server (Ubuntu)
- Created Ubuntu Server VM in VirtualBox
- Allocated:
  - 4 CPU cores
  - 8 GB RAM
  - 50 GB disk
- Installed Ubuntu Server (manual install to avoid CLI-only issues)
- Updated system packages

### Windows Domain Environment
- Deployed:
  - Windows Server 2022 (Domain Controller)
  - Windows 11 Client 01
  - Windows 11 Client 02
- Allocated 8 GB RAM and 4 CPU cores to each VM
- Created local accounts during install (bypassed internet requirement)

---

## Phase 2: Network Configuration

### Internal Network Design
- Created VirtualBox Internal Network: `adlabnet`
- Assigned static IPs:

| System             | IP Address        |
|--------------------|------------------|
| Domain Controller  | 192.168.100.10   |
| Client 01          | 192.168.100.20   |
| Client 02          | 192.168.100.21   |
| Wazuh Server       | 192.168.100.13   |

- Subnet: 255.255.255.0
- No default gateway (fully isolated)

### Wazuh Server Multi-NIC Setup
- Adapter 1: NAT (for package installation)
- Adapter 2: Host-only (host access)
- Adapter 3: Internal Network (`adlabnet`)

---

## Phase 3: Domain Controller Setup

- Installed Active Directory Domain Services (AD DS)
- Promoted server to Domain Controller
- Configured DNS role
- Created domain users and organizational units (OUs)
- Joined Windows clients to the domain

---

## Phase 4: Wazuh Installation

- Installed Wazuh server on Ubuntu VM
- Verified services:
  - Wazuh Manager
  - Wazuh Dashboard
- Confirmed dashboard access via HTTPS (port 443)

### Firewall Configuration
- Allowed required ports:
  - 1514 (agent communication)
  - 1515 (agent enrollment)
  - 443 (dashboard access)

---

## Phase 5: Agent Deployment

### Challenge: No Internet Access in Lab
- Windows VMs could not download Wazuh agent directly
- Solution:
  - Downloaded MSI installer on host machine
  - Transferred via shared folder to VMs

### Installation Steps
- Installed Wazuh agent on:
  - Domain Controller
  - Client 01
  - Client 02

### Configuration
- Updated agent config:
  - Set manager IP: `192.168.100.13`

### Issue Encountered
- Agent failed to start due to invalid config:


  <address>0.0.0.0</address>
  

### Resolution

* Corrected to:

  
  <address>192.168.100.13</address>
  

* Restarted service successfully

---

## Phase 6: Agent Enrollment

* Registered agents with Wazuh manager
* Verified connectivity:

  * Agents appeared in dashboard
  * Status: Active

---

## Phase 7: Validation

### Connectivity Testing

* Verified ping between all systems
* Confirmed agent-to-manager communication

### Dashboard Verification

* Confirmed:

  * Log ingestion
  * Endpoint visibility
  * Security events appearing

---

## Issues Encountered & Resolved

### 1. DNS Resolution Failures

* Cause: Misconfigured DNS settings in isolated network
* Fix: Pointed clients to Domain Controller DNS

---

### 2. Wazuh Server Network Issues

* Cause: Missing IP on internal adapter
* Fix: Configured static IP via Netplan

---

### 3. Agent Service Failure

* Cause: Invalid server address (0.0.0.0)
* Fix: Updated to correct manager IP

---

### 4. VM Display Issues (Black Screen)

* Cause: Graphics configuration in VirtualBox
* Fix:

  * Increased video memory
  * Restarted display manager

---

## Key Takeaways

* Isolated lab environments require manual handling of dependencies (no internet fallback)
* Proper DNS configuration is critical for domain functionality
* Wazuh agent configuration must be precise or service will fail silently
* Incremental troubleshooting is more effective than enabling all features at once

---

## Future Improvements

* Deploy Sysmon for advanced telemetry
* Implement alert tuning and detection rules
* Automate agent deployment via Group Policy
* Expand monitoring coverage across additional endpoints

---

# 💥 Why this works

This document shows:

- You can **design systems**
- You can **troubleshoot real issues**
- You understand **networking + AD + SIEM together**
- You didn’t just follow a YouTube tutorial