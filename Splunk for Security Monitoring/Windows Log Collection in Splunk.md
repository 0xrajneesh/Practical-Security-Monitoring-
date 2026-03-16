# Windows Log Collection in Splunk

## Overview

Windows systems generate important logs that record **authentication activity, system events, process execution, and security changes**. Collecting these logs centrally in **Splunk** enables security teams to monitor user behavior, detect suspicious activity, and investigate incidents.

Windows log collection is commonly performed using the **Splunk Universal Forwarder**, which reads Windows Event Logs and forwards them to a **Splunk Indexer** for analysis.

---

# Architecture

The basic architecture for Windows log collection is:

```
Windows Host → Splunk Universal Forwarder → Splunk Indexer → Splunk Search Head
```

- **Windows Host** generates system and security events
- **Universal Forwarder** collects Windows event logs
- **Indexer** stores and indexes log data
- **Search Head** enables search, dashboards, and investigations

---

# Important Windows Event Logs

Windows logs are stored in the **Windows Event Viewer** and are divided into multiple categories.

| Log Name | Description |
| --- | --- |
| Security | Authentication events, logins, privilege changes |
| System | OS and system service events |
| Application | Application activity and errors |
| Setup | System installation and update events |
| PowerShell | PowerShell command execution |

Some important security events include:

| Event ID | Description |
| --- | --- |
| 4624 | Successful login |
| 4625 | Failed login attempt |
| 4688 | Process creation |
| 4720 | User account created |
| 4728 | User added to privileged group |
| 4672 | Special privileges assigned |

These events help detect **brute-force attacks, privilege escalation, and suspicious processes**.

---

# Install Splunk Universal Forwarder

Download the **Splunk Universal Forwarder for Windows** from the Splunk website.

Install using the MSI installer.

Example silent installation:

```
msiexec.exe/isplunkforwarder.msiAGREETOLICENSE=Yes/quiet
```

Start the Splunk service after installation.

---

# Configure Forwarding

Add the Splunk indexer as the receiving server.

```
"C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe"addforward-server<indexer_ip>:9997
```

Example:

```
splunkaddforward-server192.168.1.10:9997
```

---

# Configure Windows Event Log Collection

Windows event logs are configured in **inputs.conf**.

Location:

```
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
```

Example configuration:

```
[WinEventLog://Security]
index=windows
disabled=0

[WinEventLog://System]
index=windows
disabled=0

[WinEventLog://Application]
index=windows
disabled=0

[WinEventLog://PowerShell]
index=windows
disabled=0
```

Restart the Splunk Forwarder service after configuration.

---

# Verifying Logs in Splunk

Use the following searches in Splunk.

**Successful Logins**

```
index=windows EventCode=4624
```

**Failed Logins**

```
index=windows EventCode=4625
```

**Process Creation**

```
index=windows EventCode=4688
```

**User Account Creation**

```
index=windows EventCode=4720
```

These searches help analysts monitor authentication activity and suspicious system behavior.

---

# Security Monitoring Use Cases

Windows log collection enables detection of:

- Brute force login attempts
- Privilege escalation activity
- Suspicious PowerShell execution
- Unauthorized user creation
- Malicious process execution

These logs are essential for **SOC monitoring and incident response**.

---

# Conclusion

Collecting Windows logs in Splunk provides centralized visibility into system and security events. Using the **Splunk Universal Forwarder**, organizations can ingest Windows event logs and build detections for authentication attacks, privilege abuse, and suspicious processes.

Centralized logging significantly improves **threat detection, investigation, and security monitoring capabilities**.
