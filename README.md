# Wazuh SIEM Homelab

## Overview
This experiment highlights the implementation of a security surveillance infrastructure that is completely isolated and relies on the Wazuh SIEM platform. This experiment uses a test environment consisting of a domain controller and several endpoints running Windows that generate logs sent to a Wazuh manager.

This security test environment is entirely isolated from any Internet connection and operates in an internal virtual network setup.

---

## Objectives
- Build a secure, isolated SOC-style monitoring environment
- Deploy Wazuh manager for centralized log collection
- Configure Windows endpoints and a domain controller as agents
- Validate endpoint connectivity and telemetry ingestion
- Simulate real-world troubleshooting scenarios in a segmented network

---

## Architecture

- Wazuh Manager VM (Ubuntu)
- Windows Domain Controller VM (Named GumChewer)
- Windows Client VMs
- Internal-only network (192.168.100.0/24)

All communication is restricted to internal virtual networking.

---

## Key Features Implemented
- Wazuh agent deployment across Windows systems
- Centralized log aggregation and dashboard monitoring
- Security Event Channel ingestion from Windows hosts
- File Integrity Monitoring (FIM)
- Security Configuration Assessment (SCA)
- Active endpoint telemetry collection

---

## Challenges Solved
- Internal network isolation with no external internet access
- DNS and routing configuration issues in VM environment
- Wazuh agent installation and configuration errors
- Endpoint connectivity and service startup failures

---

## Screenshots

See `/screenshots` for full build documentation:
- VM creation and setup
- Network configuration
- Agent deployment
- Dashboard validation
- Troubleshooting steps

---

## Tools Used
- Wazuh SIEM
- VirtualBox
- Windows Server (Domain Controller)
- Ubuntu Server
- PowerShell
- Event Viewer / Windows Event Channels

---

## Future Improvements
- Sysmon integration for advanced telemetry
- MITRE ATT&CK mapping
- Custom detection rules and alert tuning
- Automated agent deployment via GPO
