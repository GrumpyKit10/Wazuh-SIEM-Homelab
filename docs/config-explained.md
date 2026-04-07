# Wazuh Agent Configuration (Windows)

## Overview
This is a simplified version of the out-of-the-box Wazuh agent configuration tailored for stability and providing valuable security data in the context of a laboratory virtual environment.

---

## Key Decisions

### Manager Connection
The agents were configured to connect to the Wazuh manager running on 192.168.100.13 through the TCP port 1514 inside a private network.

---

### Event Log Collection
The following Windows Event Channels are monitored:

- Security → Authentication and security events
- System → OS-level events
- Application → Application logs

The default filtering process of events was disabled in order to gain complete visibility.

---

### File Integrity Monitoring (FIM)
To prevent performance issues observed during testing, monitoring scope was reduced to:

- C:\ProgramData
- Desktop directory

Detection of persistence techniques is made possible without compromising stability.

---

### Security Configuration Assessment (SCA)
SCA is turned on to test whether the system meets certain benchmarks for security.

---

## Lessons Learned

- excessive monitoring of files may lead to decreased performance in virtualized settings.
- Excessive filtering of Windows Security Logs may prevent essential alerts from being detected.
- Incremental deployment offers increased stability and troubleshooting capabilities.