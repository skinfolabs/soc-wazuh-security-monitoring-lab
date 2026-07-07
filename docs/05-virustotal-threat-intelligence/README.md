# VirusTotal Threat Intelligence

This chapter integrates VirusTotal with Wazuh so file-monitoring alerts can be enriched with external reputation data. VirusTotal checks files, URLs, domains, and IP addresses against many security engines and community intelligence sources.

## Technical Context

Threat intelligence adds context to raw alerts. A file-change event may only show that something changed; enrichment can add reputation data that helps decide whether it deserves priority review.

VirusTotal checks files, URLs, domains, and IP addresses against antivirus engines and community intelligence. It can provide hashes, detections, reputation, and metadata for investigation.

When integrated with Wazuh, VirusTotal can enrich alerts automatically when file activity is detected by Wazuh syscheck. This does not replace local analysis, but it gives the analyst another layer of context for triage.

> Files and URLs submitted to VirusTotal may become visible to the security community. Sensitive, proprietary, or private data should not be uploaded.

**Implemented controls:**

- Reviewed VirusTotal as an enrichment source.
- Obtained and protected the API key.
- Configured the Wazuh manager integration block.
- Defined a Windows monitored directory to trigger file-related enrichment.

---

## Detailed Walkthrough

### Step 01 - Review VirusTotal as an Enrichment Source

VirusTotal is used as an external enrichment source for suspicious files, URLs, domains, and IPs. It helps add context to Wazuh alerts by checking whether other engines or community reports have seen the same indicator.

> A reputation lookup is not a final verdict by itself. It helps prioritize investigation, but the local endpoint behavior, file origin, process activity, network traffic, and surrounding logs still matter.

![Screenshot 017 - VirusTotal Upload Interface](../../images/05-virustotal-threat-intelligence/017-virustotal-upload-page.png)

<p><sub><strong>Screenshot 017 - VirusTotal Upload Interface:</strong> VirusTotal provides file, URL, and search workflows that can support threat-intelligence enrichment.</sub></p>

This workflow supports triage: detections can raise priority, while clean reputation still needs local behavior context.

---

### Step 02 - Obtain and Protect the VirusTotal API Key

A personal API key is generated from the VirusTotal profile page. The key is sensitive because it authenticates API requests and should not be shared or embedded in public scripts.

> API keys are credentials. They should be stored securely, redacted in screenshots, and rotated if exposed.

![Screenshot 018 - VirusTotal API Key](../../images/05-virustotal-threat-intelligence/018-virustotal-api-key.png)

<p><sub><strong>Screenshot 018 - VirusTotal API Key:</strong> The VirusTotal API key page is shown with the key blurred, confirming that the credential was obtained without exposing it publicly.</sub></p>

The evidence confirms that an API key was available for the integration while keeping the credential redacted.

---

### Step 03 - Configure VirusTotal on the Wazuh Manager

The VirusTotal integration belongs on the Wazuh manager configuration because the manager performs the alert processing and integration lookup. The corrected manager path is `/var/ossec/etc/ossec.conf`.

> The Wazuh manager integration is different from an endpoint agent setting. Placing this block on the manager keeps enrichment centralized.

```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_API_KEY</api_key>
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

```bash
sudo nano /var/ossec/etc/ossec.conf
sudo systemctl restart wazuh-manager
```

![Screenshot 019 - Wazuh VirusTotal Integration Block](../../images/05-virustotal-threat-intelligence/019-wazuh-virustotal-integration.png)

<p><sub><strong>Screenshot 019 - Wazuh VirusTotal Integration Block:</strong> The Wazuh integration block references VirusTotal and uses a redacted API key.</sub></p>

The snippet is stored in [virustotal-manager-integration.xml](virustotal-manager-integration.xml). The evidence confirms the integration block, while successful enrichment still requires a file event that triggers a Wazuh alert.

---

### Step 04 - Define the Monitored Test Directory

A directory is monitored so new file activity can trigger the Wazuh syscheck workflow and VirusTotal lookup. For a Windows endpoint in this lab, the corrected monitored test path is `C:\Users\dell\virustotaltest`.

> The monitored path must match the endpoint operating system. A Linux path can be valid on Linux agents, while a Windows endpoint needs a Windows path.

```xml
<syscheck>
  <directories realtime="yes">C:\Users\dell\virustotaltest</directories>
</syscheck>
```

![Screenshot 020 - VirusTotal Monitored Directory Syntax](../../images/05-virustotal-threat-intelligence/020-virustotal-monitored-directory.png)

<p><sub><strong>Screenshot 020 - VirusTotal Monitored Directory Syntax:</strong> The screenshot demonstrates the Wazuh syscheck directory syntax used to trigger VirusTotal lookups when files appear in a monitored path.</sub></p>

The corrected Windows snippet is stored in [virustotal-windows-syscheck.xml](virustotal-windows-syscheck.xml). The screenshot confirms the monitoring concept; the public configuration uses the Windows path appropriate for this endpoint lab.

---

## Validation and Summary

The API key is obtained, the Wazuh manager integration block is configured, and a monitored test directory is defined. A complete validation would add a safe test file and confirm the resulting VirusTotal-enriched alert in Wazuh.

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
