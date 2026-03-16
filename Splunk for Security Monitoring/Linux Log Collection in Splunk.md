# Linux Log Collection in Splunk

## Overview

Linux systems generate critical logs that help detect **security incidents, authentication activity, system errors, and configuration changes**. Centralizing these logs in **Splunk** enables security teams to monitor systems, investigate incidents, and build detection rules.

Linux log collection is typically done using the **Splunk Universal Forwarder**, which reads log files from the host and securely forwards them to a **Splunk Indexer**.

---

# Architecture

The basic architecture for Linux log collection is:

```
Linux Server → Splunk Universal Forwarder → Splunk Indexer → Splunk Search Head
```

- **Linux Host** generates system and security logs
- **Universal Forwarder** collects and forwards logs
- **Indexer** stores and indexes logs
- **Search Head** allows analysts to search and analyze data

---

# Important Linux Logs

Most Linux logs are stored in **/var/log**.

| Log File | Purpose |
| --- | --- |
| /var/log/auth.log | Authentication and SSH login activity |
| /var/log/syslog | General system events |
| /var/log/messages | System messages (RHEL based systems) |
| /var/log/audit/audit.log | Detailed audit logs |
| /var/log/cron | Scheduled job execution |
| /var/log/dpkg.log | Package installation logs |

These logs help detect **login attacks, privilege escalation, persistence, and suspicious system activity**.

---

# Install Splunk Universal Forwarder

Download and install the forwarder on the Linux host.

Example for **Ubuntu/Debian**:

```
wget-O splunkforwarder.deb https://download.splunk.com/products/universalforwarder/releases/<version>/linux/splunkforwarder.deb
sudo dpkg-i splunkforwarder.deb
```

Start Splunk Forwarder:

```
sudo /opt/splunkforwarder/bin/splunkstart
```

Enable boot start:

```
sudo /opt/splunkforwarder/bin/splunk enable boot-start
```

---

# Configure Forwarding

Add the Splunk indexer:

```
sudo /opt/splunkforwarder/bin/splunk add forward-server <indexer_ip>:9997
```

Example:

```
sudo /opt/splunkforwarder/bin/splunk add forward-server192.168.1.10:9997
```

---

# Configure Log Inputs

Edit **inputs.conf**:

```
/opt/splunkforwarder/etc/system/local/inputs.conf
```

Example configuration:

```
[monitor:///var/log/auth.log]
sourcetype=linux_secure
index=linux

[monitor:///var/log/syslog]
sourcetype=syslog
index=linux

[monitor:///var/log/audit/audit.log]
sourcetype=linux_audit
index=linux
```

Restart the forwarder:

```
sudo /opt/splunkforwarder/bin/splunkrestart
```

---

# Verifying Logs in Splunk

Example searches:

**SSH Login Activity**

```
index=linux sourcetype=linux_secure ssh
```

**Failed Login Attempts**

```
index=linux "Failed password"
```

**Sudo Activity**

```
index=linux sudo
```

These queries help identify suspicious authentication behavior.

---

# Security Use Cases

Linux logs in Splunk support detection of:

- SSH brute force attacks
- Privilege escalation via sudo
- Unauthorized user creation
- Suspicious cron jobs
- Unauthorized software installation

---

# Conclusion

Collecting Linux logs in Splunk provides centralized visibility into system and security events. Using the **Splunk Universal Forwarder**, organizations can monitor authentication activity, detect attacks, and support incident investigations more effectively.
