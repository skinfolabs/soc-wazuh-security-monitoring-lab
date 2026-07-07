# Suricata IDS Integration

This chapter integrates Suricata with Wazuh so network IDS alerts can be reviewed in the SIEM. Suricata inspects traffic and writes structured EVE JSON logs that the Wazuh agent can collect.

## Technical Context

Intrusion Detection Systems and Intrusion Prevention Systems inspect traffic for known threats, suspicious patterns, indicators of compromise, and policy violations. The key difference is what happens after detection.

An IDS, or Intrusion Detection System, inspects traffic and generates alerts or logs when it finds suspicious activity. IDS detection can use signatures and rules, known indicators such as hashes, domains, URLs, TLS or SSH fingerprints, protocol anomalies, and scripting logic such as Lua-based detection. An IDS is mainly visibility-focused: it tells the analyst what happened, but it does not normally block the traffic.

An IPS, or Intrusion Prevention System, is enforcement-focused. It can allow, drop, or reject traffic based on inspection results. IPS mode is more powerful but also riskier because poorly tuned rules can block legitimate traffic.

Suricata is an open-source network threat detection engine that can operate as IDS or IPS. In this lab it is used mainly as an IDS sensor. EVE JSON is the structured Suricata output format, making alert signature, source IP, destination IP, protocol, and event type easier for Wazuh to parse.

## IDS vs. IPS Overview

| Technology | Main Role | How It Works | Lab Meaning |
|------------|-----------|--------------|-------------|
| IDS | Detects suspicious traffic and generates alerts | Uses signatures, rules, IoCs, protocol indicators, TLS/SSH fingerprints, anomalies, and scripting logic | Provides visibility into suspicious traffic without blocking it |
| IPS | Detects and enforces traffic decisions | Can allow, drop, or reject traffic based on inspection results | Useful for prevention, but requires careful tuning to avoid blocking legitimate traffic |

In this project, Suricata is treated as an IDS sensor. That means the main goal is to generate network-security evidence and forward it into Wazuh, not to prove live blocking.

**Implemented controls:**

- Installed Suricata and verified Npcap packet-capture support.
- Enabled EVE JSON logging in `suricata.yaml`.
- Started Suricata on the selected interface.
- Configured the Wazuh agent to read Suricata `eve.json`.
- Validated Suricata events in Wazuh Threat Hunting.

---

## Detailed Walkthrough

### Step 01 - Install Suricata and Verify Npcap

Suricata is installed on Windows under `C:\Program Files\Suricata\`. Key components include `suricata.exe`, `suricata.yaml`, `rules\`, and `log\`. Npcap is required so Suricata can capture packets from the network interface.

> Packet capture is the sensor layer for IDS visibility. If Npcap is missing or stopped, Suricata may be installed but unable to inspect live traffic.

```powershell
Get-Service -Name npcap
Start-Service -Name npcap
Get-NetAdapter | Select Name, Status
```

The commands verify that Npcap is running and identify the interface name that Suricata should monitor.

The interface name is important because Suricata must listen on the adapter that actually sees the lab traffic. A wrong interface can produce empty logs even when traffic is active elsewhere.

---

### Step 02 - Enable EVE JSON Logging

Suricata is configured to write EVE JSON output to `eve.json`. Other logs such as `fast.log` and `stats.log` can be useful, but `eve.json` is the main file for Wazuh ingestion because it is structured JSON.

> JSON logs are easier for SIEM platforms to parse than plain text logs. This makes dashboards, filters, and detection rules more reliable.

```yaml
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
```

![Screenshot 014 - Suricata EVE JSON Logging](../../images/04-suricata-ids-integration/014-suricata-eve-json-config.png)

<p><sub><strong>Screenshot 014 - Suricata EVE JSON Logging:</strong> suricata.yaml is configured to write EVE JSON output, which becomes the log file collected by Wazuh.</sub></p>

The screenshot confirms the EVE JSON output configuration. The full snippet is stored in [suricata-eve-json.yaml](suricata-eve-json.yaml).

---

### Step 03 - Run Suricata on the Selected Interface

Suricata is started from an administrator PowerShell session using the configuration file and the selected interface name.

> This step activates the IDS sensor. Suricata reads `suricata.yaml`, loads the configured rule files, listens on the selected adapter, and writes detected events to its log directory.

```powershell
cd "C:\Program Files\Suricata\"
suricata -c suricata.yaml -i <interface>
```

The command starts Suricata with the configured rule set and output options.

---

### Step 04 - Configure Wazuh Agent to Read EVE JSON

The Wazuh agent is configured with a `localfile` entry that points to Suricata's `eve.json`. The log format is set to `json` so Wazuh treats the file as structured data.

> Localfile collection lets Wazuh monitor application logs that are not native Windows Event Logs. This is how Suricata events become searchable in Wazuh Threat Hunting.

```xml
<localfile>
  <log_format>json</log_format>
  <location>C:\Program Files\Suricata\log\eve.json</location>
</localfile>
```

```powershell
Restart-Service -Name WazuhSvc
```

![Screenshot 015 - Wazuh Localfile for Suricata EVE](../../images/04-suricata-ids-integration/015-wazuh-agent-suricata-localfile.png)

<p><sub><strong>Screenshot 015 - Wazuh Localfile for Suricata EVE:</strong> The Wazuh agent configuration points to the Suricata EVE JSON log file.</sub></p>

The screenshot confirms the localfile path used by Wazuh. The full snippet is stored in [suricata-wazuh-localfile.xml](suricata-wazuh-localfile.xml).

---

### Step 05 - Generate Traffic and Validate Alerts

Test traffic is generated and the Wazuh dashboard is filtered for Suricata-related events. The goal is to confirm the ingestion path.

> A test command validates the pipeline, not full detection coverage. More realistic attacks or known test signatures would be required to validate a production IDS policy.

```powershell
ping google.com
```

![Screenshot 016 - Suricata Alerts in Wazuh](../../images/04-suricata-ids-integration/016-suricata-alert-wazuh.png)

<p><sub><strong>Screenshot 016 - Suricata Alerts in Wazuh:</strong> Wazuh Threat Hunting displays Suricata events, confirming that Suricata logs are reaching the SIEM.</sub></p>

The evidence confirms Suricata-to-Wazuh log visibility. It does not prove that every Suricata rule is tuned or that IPS blocking is enabled.

---

## Validation and Summary

Suricata writes EVE JSON, Wazuh reads the log file, and Suricata events appear in Wazuh Threat Hunting. This validates the IDS log-ingestion path, while production IDS coverage would still require tuned rules, realistic traffic, and false-positive review.

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
