# Final Summary

The lab demonstrates a complete SOC monitoring workflow using Wazuh as the central platform. Endpoint, firewall, IDS, threat-intelligence, file-integrity, Sysmon, and authentication-failure data are documented and connected into a single SOC monitoring project.

The result is a functional lab workflow, not a production deployment. Several items still require deeper production validation, including live pfSense decoder matching, a safe VirusTotal enrichment trigger, and tuned Suricata rules against realistic traffic.

## Validation Summary

| Area | Confirmed Result |
|------|------------------|
| Wazuh server | Dashboard became accessible after required services were checked and started. |
| Wazuh agent | Windows endpoint appeared as an active agent in Wazuh. |
| pfSense | Firewall was configured for remote logging toward Wazuh. |
| Suricata | EVE JSON output was configured and Suricata events appeared in Wazuh. |
| VirusTotal | API key was obtained, redacted, and integrated through the Wazuh manager configuration. |
| FIM | File creation, modification, and deletion events appeared in Wazuh for the monitored path. |
| Sysmon | Sysmon installation and EventChannel ingestion configuration were documented. |
| Brute force | Controlled Hydra SSH attempts produced Wazuh failed-logon alerts and Windows Event 4625 evidence. |

## Production Recommendations

- Replace all default credentials immediately after initial lab access.
- Restrict pfSense management access to trusted lab networks.
- Validate Wazuh custom decoders with real sample logs before relying on rule severity.
- Generate a safe VirusTotal-enriched alert to validate the complete lookup path.
- Tune Suricata rules against realistic traffic and review false positives before production use.
- Protect VirusTotal API keys and avoid uploading sensitive files.
- Keep Sysmon configuration tuned to collect high-value events without overwhelming the SIEM.
- Monitor for repeated failed logons, account lockouts, SSH anomalies, and unexpected file changes.
- Add multi-factor authentication, rate limiting, and account lockout policies to reduce brute-force risk.

## Skills Demonstrated

- Wazuh SIEM/XDR deployment
- Endpoint agent onboarding
- Firewall syslog forwarding
- Suricata IDS log ingestion
- VirusTotal threat-intelligence enrichment
- File integrity monitoring
- Sysmon and Windows Event Log collection
- Hydra brute-force simulation
- Windows Event 4625 analysis
- Evidence-based SOC validation and defensive recommendation writing

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
