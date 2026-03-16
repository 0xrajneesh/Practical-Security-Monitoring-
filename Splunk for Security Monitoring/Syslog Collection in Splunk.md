# Syslog Collection in Splunk

## Overview

**Syslog** is a standard logging protocol used by many systems such as **Linux servers, network devices, firewalls, and security appliances** to send log messages to a centralized server. Collecting syslog data in **Splunk** allows security teams to monitor infrastructure activity, detect suspicious behavior, and perform incident investigations.

In a typical setup, systems send logs to a **Splunk syslog receiver**, which then indexes the logs for analysis.

---

# Architecture

The basic architecture for syslog collection is:

```
Log Source (Linux / Firewall / Network Device)
                │
                ▼
           Syslog Server
                │
                ▼
        Splunk Indexer
                │
                ▼
        Splunk Search Head
```

- **Log Sources** generate syslog messages
- **Syslog Server** receives logs over UDP/TCP (port 514)
- **Splunk Indexer** stores and indexes the logs
- **Search Head** allows analysts to search and investigate events

---

# Common Syslog Sources

Many infrastructure devices use syslog for logging.

| Source | Example Logs |
| --- | --- |
| Linux Servers | Authentication, services, system activity |
| Firewalls | Allowed/blocked network connections |
| Routers & Switches | Network traffic and configuration changes |
| IDS/IPS | Security alerts and intrusion attempts |
| VPN Gateways | Remote access activity |

These logs help detect **network attacks, unauthorized access, and configuration changes**.

---

# Configure Syslog Input in Splunk

Splunk can receive syslog data directly using **UDP or TCP inputs**.

### Enable Syslog Port

Edit **inputs.conf** on the Splunk server.

Location:

```
$SPLUNK_HOME/etc/system/local/inputs.conf
```

Example configuration:

```
[udp://514]
sourcetype=syslog
index=syslog

[tcp://514]
sourcetype=syslog
index=syslog
```

This configuration allows Splunk to receive syslog messages on **port 514**.

Restart Splunk after configuration.

```
splunk restart
```

---

# Configure Log Sources

Configure the log source to send logs to the Splunk server.

Example **Linux syslog forwarding** configuration.

Edit the rsyslog configuration file:

```
/etc/rsyslog.conf
```

Add the following line:

```
*.* @<splunk_server_ip>:514
```

Example:

```
*.* @192.168.1.10:514
```

Restart rsyslog:

```
sudo systemctl restart rsyslog
```

Logs will now be forwarded to Splunk.

---

# Verifying Syslog Data in Splunk

Search for incoming syslog logs.

```
index=syslog sourcetype=syslog
```

Example searches:

**SSH Activity**

```
index=syslog ssh
```

**Authentication Failures**

```
index=syslog "Failed password"
```

**Firewall Events**

```
index=syslog deny OR blocked
```

These queries help identify suspicious activity across infrastructure devices.

---

# Security Monitoring Use Cases

Syslog data enables detection of several security events.

- Unauthorized login attempts on servers
- Network scanning or blocked connections
- Suspicious firewall activity
- VPN login attempts
- Configuration changes on network devices

This provides centralized visibility across the infrastructure.
