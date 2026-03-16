# Splunk File Extraction using RegEx

## Overview

Many logs do not follow a structured format like CSV or delimited files. Instead, they contain **free-text events** where important information such as IP addresses, usernames, or actions must be extracted using **Regular Expressions (RegEx)**.

Splunk allows field extraction using RegEx so analysts can convert raw log data into **structured fields** that are searchable and useful for detection, dashboards, and investigations.

---

# Example Log File

Example log entries:

```
2026-03-10 10:21:30 User raj logged in from 192.168.1.10 status=success
2026-03-10 10:22:12 User admin failed login from 192.168.1.20 status=failed
```

These logs contain useful information such as:

- Timestamp
- Username
- Source IP
- Login status

However, the fields are embedded in text and must be extracted using **RegEx**.

---

# Configure File Monitoring

First configure Splunk to monitor the log file.

Edit **inputs.conf**

```
$SPLUNK_HOME/etc/system/local/inputs.conf
```

Example configuration:

```
[monitor:///var/log/user_activity.log]
index=security
sourcetype=user_activity
disabled=0
```

This allows Splunk to ingest the log file.

---

# Configure RegEx Field Extraction

Field extraction using RegEx is configured in **props.conf**.

```
$SPLUNK_HOME/etc/system/local/props.conf
```

Example configuration:

```
[user_activity]
REPORT-user-fields = user_extraction
```

Next define the RegEx extraction in **transforms.conf**.

```
$SPLUNK_HOME/etc/system/local/transforms.conf
```

Example configuration:

```
[user_extraction]
REGEX = User\s(?<username>\w+).*from\s(?<source_ip>\d+\.\d+\.\d+\.\d+).*status=(?<status>\w+)
FORMAT = username::$1 source_ip::$2 status::$3
```

Explanation:

- **REGEX** identifies patterns in the log
- **username**, **source_ip**, and **status** are extracted fields
- Named capture groups `(?<fieldname>)` define field names

---

# RegEx Breakdown

Example RegEx:

```
User\s(?<username>\w+)
```

Explanation:

| Pattern | Meaning |
| --- | --- |
| User\s | Matches the word "User " |
| (?<username>\w+) | Extracts the username |

Example IP extraction:

```
(?<source_ip>\d+\.\d+\.\d+\.\d+)
```

This extracts IPv4 addresses from the log.

---

# Verifying Field Extraction

After restarting Splunk, search the data:

```
index=security sourcetype=user_activity
```

Example searches:

**Failed logins**

```
index=security status=failed
```

**Logins from specific IP**

```
index=security source_ip=192.168.1.20
```

**Activity by user**

```
index=security username=admin
```

The extracted fields will now appear in the Splunk field list.

---

# Use Cases

RegEx extraction is useful for parsing:

- Application logs
- Custom security logs
- Authentication logs
- IDS/IPS alerts
- Web server logs
- Threat intelligence feeds

This allows analysts to extract meaningful fields from **unstructured logs**

---

---

# Conclusion

RegEx-based extraction allows Splunk to parse **unstructured log data** and convert important information into searchable fields. By defining patterns in **props.conf** and **transforms.conf**, organizations can extract usernames, IP addresses, actions, and other security-relevant data for better monitoring and analysis.
