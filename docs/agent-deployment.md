# Wazuh Agent Deployment Guide – Windows Endpoints

## Overview
This document outlines the process used to deploy and configure Wazuh agents across Windows systems in an isolated virtual lab environment. The deployment includes a Domain Controller and multiple Windows clients connected to a centralized Wazuh SIEM manager.

---

## Environment Summary

| System              | Role               | IP Address        |
|--------------------|-------------------|------------------|
| Wazuh Manager       | SIEM Server        | 192.168.100.13   |
| Domain Controller   | AD + DNS Server    | 192.168.100.10   |
| Client 01           | Windows Endpoint   | 192.168.100.20   |
| Client 02           | Windows Endpoint   | 192.168.100.21   |

---

## Deployment Method

Because the lab environment is fully isolated from the internet, agents were installed manually.

### Step 1: Obtain Installer
- Downloaded Wazuh Windows agent MSI from official source on host machine
- Transferred installer into VM using shared folder

---

### Step 2: Install Agent (Windows)

Executed installer on each endpoint:

powershell
msiexec.exe /i wazuh-agent.msi /q

---

### Step 3: Configure Manager Connection

Edited agent configuration file:

C:\Program Files (x86)\ossec-agent\ossec.conf


Set Wazuh manager IP:

<client>
  <server>
    <address>192.168.100.13</address>
    <port>1514</port>
    <protocol>tcp</protocol>
  </server>
</client>

---

### Step 4: Start Agent Service

powershell
NET START Wazuh

---

## Enrollment Process

After installation, agents were registered with the Wazuh manager using the agent registration process.

* Verified agent key assignment
* Confirmed connectivity from manager dashboard
* Ensured agents transitioned to "Active" state

---

## Issues Encountered & Fixes

### Issue 1: Agent Service Failed to Start

**Error:**


Invalid server address found: '0.0.0.0'


**Cause:**
Default or misconfigured manager IP in ossec.conf

**Fix:**
Updated configuration to correct manager IP:


192.168.100.13

---

### Issue 2: DNS and Connectivity Confusion

**Cause:**
Initial misconfiguration of network isolation settings

**Fix:**
Ensured all endpoints used static IPs and correct internal subnet:


192.168.100.0/24

---

### Issue 3: Deployment Constraints (No Internet Access)

**Cause:**
Lab is intentionally isolated

**Fix:**

* Manual MSI transfer via host machine
* No reliance on external package managers

---

## Validation Steps

* Confirmed service status:

powershell
Get-Service Wazuh


* Verified agent connectivity in Wazuh dashboard
* Confirmed log ingestion from:

  * Security Event Log
  * System Event Log
  * Application Event Log

---

## Security Notes

* Agents communicate only over internal network
* No direct internet access from endpoints
* All telemetry is centralized through Wazuh manager
* Communication secured via TCP 1514/1515

---

## Key Takeaways

* Manual deployment is required in isolated environments
* Small configuration errors (like incorrect IPs) fully break agent communication
* Centralized logging depends heavily on consistent network configuration
* Validation through both CLI and dashboard is essential

---

## Future Improvements

* Automate deployment via Group Policy (GPO)
* Integrate Sysmon for deeper endpoint visibility
* Use scripted MSI deployment via PowerShell remoting
* Add certificate-based agent authentication
