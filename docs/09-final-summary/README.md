# Final Summary

This chapter summarizes the confirmed results, evidence limits, skills demonstrated, and recommended improvements for the Wazuh complete SOC lab.

---

## Confirmed Results

| Area | Confirmed Result |
|------|------------------|
| Wazuh server | Dashboard became accessible after required services were checked and started. |
| Wazuh agent | Windows endpoint appeared as an active agent in Wazuh. |
| pfSense | Firewall was configured for remote logging toward Wazuh. |
| Suricata | EVE JSON output was configured and Suricata events appeared in Wazuh. |
| VirusTotal | API key was obtained, redacted, and integrated through the Wazuh manager configuration. |
| FIM | File creation, modification, and deletion events appeared in Wazuh for the monitored path. |
| Sysmon | Sysmon installation and EventChannel ingestion configuration were documented. |
| Brute force | Hydra SSH attempts produced Wazuh failed-logon alerts and Windows Event 4625 evidence. |

## Evidence Limits

- pfSense remote logging is configured, but live decoder matching should be tested with real `filterlog` samples.
- VirusTotal integration is configured, but a final enriched alert should be generated with a safe test file to validate the complete lookup path.
- Suricata alert visibility is shown, but production IDS tuning would require rule validation, false-positive review, and defined monitoring scope.
- The brute-force attack was a controlled lab simulation, not evidence of unauthorized activity in a production environment.

## Defensive Recommendations

- Replace all default credentials immediately after initial lab access.
- Restrict pfSense management access to trusted lab networks.
- Validate Wazuh custom decoders with real sample logs before relying on rule severity.
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
- SOC-style evidence interpretation and defensive recommendations

## Final Result

The lab demonstrates a complete SOC monitoring workflow using Wazuh as the central platform. Endpoint, firewall, IDS, threat-intelligence, file-integrity, Sysmon, and authentication-failure data are documented and connected into a single portfolio project.

---

## Project Chapters

| Chapter | Description |
|---------|-------------|
| [Project Overview](../01-project-overview/README.md) | Scenario, architecture, tools, and lab traffic flow |
| [Wazuh Server and Agent Onboarding](../02-wazuh-server-agent-onboarding/README.md) | Wazuh OVA deployment, dashboard access, service recovery, and Windows agent registration |
| [pfSense Log Integration](../03-pfsense-log-integration/README.md) | Firewall VM setup, remote syslog forwarding, and Wazuh decoder/rule logic |
| [Suricata IDS Integration](../04-suricata-ids-integration/README.md) | Suricata installation, EVE JSON logging, Wazuh ingestion, and alert validation |
| [VirusTotal Threat Intelligence](../05-virustotal-threat-intelligence/README.md) | API key handling, Wazuh manager integration, and monitored directory enrichment |
| [File Integrity Monitoring](../06-file-integrity-monitoring/README.md) | Windows FIM configuration and file create/modify/delete alert validation |
| [Sysmon Log Ingestion](../07-sysmon-log-ingestion/README.md) | Windows Event Log concepts, Sysmon installation, and EventChannel ingestion |
| [SSH Brute Force Detection](../08-ssh-brute-force-detection/README.md) | Hydra simulation, Wazuh detection, Windows Event 4625 analysis, and defensive controls |
| [Final Summary](../09-final-summary/README.md) | Results, limitations, skills, and hardening recommendations |
