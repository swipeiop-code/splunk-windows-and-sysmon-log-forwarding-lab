# Splunk Windows & Sysmon Log Forwarding Lab

## Project Overview

This project demonstrates the setup of a basic SOC home lab using Splunk Enterprise and Splunk Universal Forwarder to collect and analyze Windows logs.

The objective was to forward Windows Event Logs and Sysmon logs from a Windows 10 system to Splunk Enterprise running on a MacBook Air M1 and troubleshoot ingestion issues during the setup process.

---

# Architecture

Windows 10 Laptop
↓
Splunk Universal Forwarder
↓
Splunk Enterprise on MacBook Air M1

---

# Environment Details

## Splunk Server
- Device: MacBook Air M1
- Software: Splunk Enterprise

## Forwarder Client
- Device: Windows 10 Laptop
- Software: Splunk Universal Forwarder
- Sysmon Installed

---

# Project Goals

- Configure Splunk Enterprise
- Configure Splunk Universal Forwarder
- Forward Windows Event Logs
- Forward Sysmon Logs
- Troubleshoot log ingestion issues
- Understand SIEM log pipelines

---

# Splunk Enterprise Setup

1. Installed Splunk Enterprise on macOS
2. Configured Splunk Web
3. Created an index named:

```text
windows
```

4. Enabled receiving on port:

```text
9997
```

---

# Universal Forwarder Setup

1. Installed Splunk Universal Forwarder on Windows 10
2. Configured forwarding using:

## outputs.conf

```ini
[tcpout]
defaultGroup = splunk_group

[tcpout:splunk_group]
server = <YOUR_MAC_IP>:9997
```

3. Configured Windows log inputs

## inputs.conf

```ini
[WinEventLog://Security]
disabled = 0
index = windows

[WinEventLog://System]
disabled = 0
index = windows

[WinEventLog://Application]
disabled = 0
index = windows
```

4. Restart

To verify that the Splunk Universal Forwarder was functioning correctly with the updated configuration settings:

```cmd
"C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" restart
```

---

# Verifying Forwarder Status

Checked whether the Universal Forwarder was connected and active using:

```cmd
"C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe" list forward-server
```

This command was used to verify:
- Active forwarder connections
- Connection status
- Whether forwarding to Splunk Enterprise was successful

---

# Verifying Listening Port on Splunk Enterprise

Verified Splunk was listening on port 9997 using terminal commands.

Example:

```bash
sudo lsof -i :9997
```

This confirmed Splunk Enterprise was accepting forwarded logs.

---

# Windows Event Log Forwarding

Windows logs were successfully forwarded and searchable in Splunk using searches such as:

```spl
index=windows
```

---

# Sysmon Log Forwarding

After basic Windows log forwarding worked, Sysmon log forwarding was configured.

Initial attempts failed and required extensive troubleshooting.

---

# Sysmon Troubleshooting Process

## Troubleshooting Steps Attempted

- Modified inputs.conf multiple times
- Tested Sysmon forwarding with and without RenderXml
- Restarted Splunk Universal Forwarder repeatedly
- Reviewed Splunk ingestion behavior
- Verified Sysmon event generation

---

# Sysmon Configuration

Eventually updated inputs.conf with:

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = windows
renderXml = true
```

However, Sysmon logs still did not appear in Splunk.

---

# Root Cause Investigation

Reviewed the Universal Forwarder logs located at:

```text
C:\Program Files\SplunkUniversalForwarder\var\log\splunk\splunkd.log
```

During investigation, an:

```text
Error Code: 5
```

was identified related to Sysmon log forwarding.

After resolving the permission/configuration issue causing Error Code 5, Sysmon logs successfully began forwarding into Splunk.

---

# Validation

Verified successful ingestion of:
- Windows Security Logs
- Windows System Logs
- Windows Application Logs
- Sysmon Logs

Example searches:

```spl
index=windows
```

```spl
index=windows source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
```

---

# Skills Demonstrated

- Splunk Enterprise setup
- Splunk Universal Forwarder configuration
- Windows Event Log forwarding
- Sysmon log forwarding
- SIEM troubleshooting
- Log ingestion validation
- Splunk configuration management
- Windows log analysis

---

# Lessons Learned

This project helped strengthen my understanding of:
- SIEM architecture
- Windows logging pipelines
- Sysmon integration
- Splunk troubleshooting methodology
- Forwarder-to-indexer communication
- Operational SOC workflows
