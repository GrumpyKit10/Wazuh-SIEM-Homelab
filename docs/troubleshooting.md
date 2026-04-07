# Wazuh SIEM Lab – Troubleshooting Guide

## Overview
This document records real issues encountered during the deployment and operation of a Wazuh SIEM homelab environment. It includes root causes, diagnostic steps, and resolutions.

The goal is to demonstrate practical troubleshooting ability in a multi-system security environment.

---

## Issue 1: Wazuh Agent Service Fails to Start

### Symptoms
- Windows service fails with no clear error message
- Output:

The service did not report an error.
NET HELPMSG 3534

### Root Cause
Incorrect manager IP configured in `ossec.conf`:

<address>0.0.0.0</address>

### Diagnosis Steps

* Checked service status via `NET START Wazuh`
* Reviewed agent log file:


C:\Program Files (x86)\ossec-agent\ossec.log

### Resolution

Updated configuration:

xml id="fix1"
<address>192.168.100.13</address>

Restarted service successfully.

---

## Issue 2: Agents Not Appearing in Dashboard

### Symptoms

* Agent installed but not visible in Wazuh dashboard
* No logs being received

### Root Cause

Agent was running but not properly enrolled with manager

### Resolution

* Re-registered agent with manager
* Verified correct authentication key assignment
* Restarted Wazuh manager and agent services

---

## Issue 3: DNS Resolution Failures in Domain Environment

### Symptoms

* Domain-joined clients unable to resolve hostnames
* Intermittent connectivity between systems

### Root Cause

Clients were not properly pointing to Domain Controller as DNS server

### Resolution

Set DNS manually on clients:

* Preferred DNS: `192.168.100.10`

Verified with:


nslookup wazuh-server

---

## Issue 4: Wazuh Server Installation Access Issues

### Symptoms

* Dashboard not accessible via browser
* Curl requests to localhost failed

### Root Cause

Service not fully initialized or blocked by firewall rules

### Resolution

* Verified services were running:


systemctl status wazuh-dashboard

* Confirmed ports:

  * 443 (dashboard)
  * 1514 (agent)
  * 1515 (enrollment)

* Restarted services

---

## Issue 5: VirtualBox Network Misconfiguration

### Symptoms

* VMs could not communicate across internal network
* IPs not reachable via ping

### Root Cause

Incorrect or missing internal network adapter configuration

### Resolution

* Ensured all VMs connected to same internal network name:


adlabnet

* Assigned static IPs in correct subnet:


192.168.100.0/24

---

## Issue 6: Performance Degradation / VM Freezing (Domain Controller)

### Symptoms

* Domain Controller freezing under load
* Delayed log processing

### Root Cause

Over-aggressive file integrity monitoring and event collection

### Contributing Factors

* Broad syscheck directories
* High event throughput from Security logs

### Resolution

* Reduced syscheck scope to key directories:

  * C:\ProgramData
  * User Desktop
* Deferred Sysmon integration for later tuning

---

## Key Troubleshooting Patterns Observed

* Misconfigured IP addresses cause silent failures in agents
* DNS misconfiguration breaks domain trust and name resolution
* Virtual network isolation requires strict consistency in adapter settings
* SIEM ingestion issues often stem from agent enrollment, not log generation

---

## Lessons Learned

* Always validate connectivity before assuming service failure
* Logs are the primary source of truth (not UI dashboards)
* Small configuration errors can cascade into full system failure
* Incremental system build-out is more stable than full feature deployment

---

## Improvement Plan

* Implement centralized logging validation scripts
* Add Sysmon to improve event visibility
* Introduce automated configuration validation for agents
* Create baseline "healthy system checklist" for future deployments

