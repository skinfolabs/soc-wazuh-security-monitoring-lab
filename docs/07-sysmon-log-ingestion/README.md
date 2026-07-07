# Sysmon Log Ingestion

This chapter explains Windows Event Logs, installs Sysmon, and configures Wazuh to collect Sysmon events through the Windows EventChannel.

## Technical Context

Logs provide traceability: what happened, when it happened, where it happened, and which component recorded it. In Windows, Event Logs are a primary source of this visibility.

A log entry records an event with fields such as timestamp, event ID, source, severity, and message. These fields help analysts reconstruct activity during an investigation.

Windows Event Logs include System, Application, Security, Setup, and Forwarded Events. Sysmon extends this visibility by recording detailed endpoint activity in `Microsoft-Windows-Sysmon/Operational`.

Centralizing these logs in Wazuh makes the data easier to search, correlate, and alert on. Instead of opening Event Viewer on one machine, the analyst can review endpoint events together with firewall, IDS, FIM, and authentication evidence.

**Implemented controls:**

- Reviewed Windows Event Log sources and useful event IDs.
- Installed Sysmon and applied a configuration.
- Configured Wazuh EventChannel collection for Sysmon telemetry.

---

## Detailed Walkthrough

### Step 01 - Review Windows Event Log Sources

Windows logs include System, Application, Security, Setup, and Forwarded Events. Security logs are especially important for authentication, account management, and audit activity.

> A log is the written record of an event. In incident response, timestamps, event IDs, messages, and event levels help reconstruct what happened and when. Accurate timestamps are especially important when comparing endpoint events with firewall or IDS alerts.

| Event ID | Meaning |
|----------|---------|
| `4720` | User account created |
| `4726` | User account deleted |
| `4738` | User account changed |
| `4741` | Computer account created |
| `4624` | Successful logon |
| `4625` | Failed logon |
| `4104` | PowerShell script block logging |
| `4688` | New process created |
| `2003` | USB device connected |
| `1102` | Security log cleared |

The table identifies useful Windows security and operational events for endpoint investigation. Event meaning depends on the log channel and event provider, so the event ID should always be reviewed together with its source, message, and fields.

---

### Step 02 - Install and Configure Sysmon

Sysmon, short for System Monitor, is installed from Microsoft Sysinternals and configured with a ruleset such as SwiftOnSecurity's configuration. It records endpoint behavior such as process creation, network connections, file creation time changes, and driver loading.

> Sysmon is most useful when paired with a good configuration. The configuration controls which events are logged and helps reduce noise while keeping high-value telemetry.

```powershell
sysmon.exe -i -accepteula
sysmon -c sysmonconfig.xml
```

Sysmon events can be viewed locally under:

```text
Applications and Services Logs
Microsoft
Windows
Sysmon
Operational
```

This validates local Sysmon installation before forwarding logs to Wazuh.

---

### Step 03 - Configure Wazuh EventChannel Collection

The Wazuh agent reads Sysmon events from the Windows EventChannel and forwards them to the manager.

> EventChannel collection lets Wazuh ingest structured Windows logs without needing to read raw `.evtx` files directly.

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

```powershell
Restart-Service -Name WazuhSvc
```

The snippet is stored in [sysmon-eventchannel-localfile.xml](sysmon-eventchannel-localfile.xml).

---

## Validation and Summary

The configuration prepares Wazuh to ingest Sysmon events. Validation should include checking the Wazuh dashboard for Sysmon events and confirming key fields such as event ID, process image, command line, and parent process.

---

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 0 | [Project Overview](../../README.md) | Main project overview, objectives, tools, and skills |
| 1 | [Topology and Lab Environment](../01-topology-and-lab-environment/README.md) | Lab architecture, component roles, telemetry flow, and trust boundaries |
| 2 | [Wazuh Server and Agent Onboarding](../02-wazuh-server-agent-onboarding/README.md) | Wazuh OVA access, service recovery, and Windows agent registration |
| 3 | [pfSense Log Integration](../03-pfsense-log-integration/README.md) | Firewall setup, remote syslog forwarding, and Wazuh decoder/rule logic |
| 4 | [Suricata IDS Integration](../04-suricata-ids-integration/README.md) | Suricata EVE JSON logging, Wazuh ingestion, and alert validation |
| 5 | [VirusTotal Threat Intelligence](../05-virustotal-threat-intelligence/README.md) | API key handling, Wazuh manager integration, and monitored directory enrichment |
| 6 | [File Integrity Monitoring](../06-file-integrity-monitoring/README.md) | Windows FIM configuration and file-change alert validation |
| 7 | [Sysmon Log Ingestion](../07-sysmon-log-ingestion/README.md) | Windows Event Log concepts, Sysmon setup, and EventChannel ingestion |
| 8 | [SSH Brute Force Detection](../08-ssh-brute-force-detection/README.md) | Hydra simulation, Wazuh detection, and Windows Event 4625 analysis |
| 9 | [Final Summary](../09-final-summary/README.md) | Validation summary, production recommendations, and skills demonstrated |
