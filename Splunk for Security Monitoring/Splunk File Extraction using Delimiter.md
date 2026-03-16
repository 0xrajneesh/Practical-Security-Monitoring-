# Splunk File Extraction using Delimiter

## Overview

Many log files store data in a **structured format separated by delimiters** such as commas, tabs, pipes (`|`), or semicolons. Splunk can automatically extract fields from these logs by defining the delimiter used in the file.

Delimiter-based extraction helps convert raw log data into **structured fields**, making it easier to search, filter, and analyze events.

---

# Example Delimited Log File

Example log file:

```
timestamp,username,source_ip,action,status
2026-03-10 10:21:30,raj,192.168.1.10,login,success
2026-03-10 10:22:15,admin,192.168.1.20,login,failed
```

In this example:

- **Comma ( , )** is the delimiter
- Each column represents a field such as timestamp, username, and action.

---

# Configure File Monitoring in Splunk

First, configure Splunk to monitor the log file.

Edit **inputs.conf**:

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

This tells Splunk to ingest the log file.

---

# Configure Delimiter Field Extraction

Field extraction using delimiters is configured in **props.conf**.

Location:

```
$SPLUNK_HOME/etc/system/local/props.conf
```

Example configuration:

```
[user_activity]
DELIMS=","
FIELDS=timestamp,username,source_ip,action,status
```

Explanation:

- **DELIMS** defines the delimiter used in the log file
- **FIELDS** defines the field names in order

Splunk will automatically map values from each column to the defined fields.

---

# Example with Pipe Delimiter

Example log:

```
2026-03-10 10:30:10|raj|192.168.1.5|file_access|allowed
```

Configuration:

```
[file_logs]
DELIMS="|"
FIELDS=timestamp,user,source_ip,action,status
```

This allows Splunk to extract each value as a searchable field.

---

# Verifying Extracted Fields

Search the data in Splunk:

```
index=security sourcetype=user_activity
```

Example queries:

**Login failures**

```
index=security action=login status=failed
```

**User activity from specific IP**

```
index=security source_ip=192.168.1.20
```

Fields such as `username`, `action`, and `status` will now be searchable.

---

# Use Cases

Delimiter-based field extraction is commonly used for:

- CSV security logs
- Application audit logs
- Custom authentication logs
- Network device exported logs
- SIEM training datasets

This method simplifies log parsing when logs already follow a structured format.

---

# Best Practices

- Ensure the delimiter is **consistent across all events**
- Define fields in the **correct order**
- Use clear **field names** for easier searches
- Validate extraction using the **Splunk search interface**

---

# Conclusion

Delimiter-based file extraction allows Splunk to convert structured log files into searchable fields with minimal configuration. By defining delimiters and field names, analysts can quickly analyze logs and build detections without complex parsing rules.
