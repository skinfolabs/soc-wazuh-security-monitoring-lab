# Wazuh Complete SOC Lab

![Category](https://img.shields.io/badge/Category-SOC%20Lab-blue)
![Platform](https://img.shields.io/badge/Platform-Wazuh%20%7C%20pfSense%20%7C%20Suricata-lightgrey)
![Focus](https://img.shields.io/badge/Focus-SIEM%20%7C%20XDR%20%7C%20Detection-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Overview

This project documents a complete SOC home lab built around Wazuh SIEM/XDR. The lab combines a Wazuh server, a Windows endpoint agent, pfSense firewall logging, Suricata IDS alerts, VirusTotal enrichment, file integrity monitoring, Sysmon telemetry, and an SSH brute-force detection scenario.

The goal is to show how endpoint, firewall, IDS, threat-intelligence, and Windows event data can be collected and reviewed from a central SIEM workflow. Each chapter explains the purpose of the tool, the configuration steps, the commands used, the screenshots that validate the result, and the security value of the integration.

## Project Chapters

| Chapter | Description |
|---------|-------------|
| [Project Overview](docs/01-project-overview/README.md) | Scenario, architecture, tools, and lab traffic flow |
| [Wazuh Server and Agent Onboarding](docs/02-wazuh-server-agent-onboarding/README.md) | Wazuh OVA deployment, dashboard access, service recovery, and Windows agent registration |
| [pfSense Log Integration](docs/03-pfsense-log-integration/README.md) | Firewall VM setup, remote syslog forwarding, and Wazuh decoder/rule logic |
| [Suricata IDS Integration](docs/04-suricata-ids-integration/README.md) | Suricata installation, EVE JSON logging, Wazuh ingestion, and alert validation |
| [VirusTotal Threat Intelligence](docs/05-virustotal-threat-intelligence/README.md) | API key handling, Wazuh manager integration, and monitored directory enrichment |
| [File Integrity Monitoring](docs/06-file-integrity-monitoring/README.md) | Windows FIM configuration and file create/modify/delete alert validation |
| [Sysmon Log Ingestion](docs/07-sysmon-log-ingestion/README.md) | Windows Event Log concepts, Sysmon installation, and EventChannel ingestion |
| [SSH Brute Force Detection](docs/08-ssh-brute-force-detection/README.md) | Hydra simulation, Wazuh detection, Windows Event 4625 analysis, and defensive controls |
| [Final Summary](docs/09-final-summary/README.md) | Results, limitations, skills, and hardening recommendations |

## Lab Environment Summary

| Component | Role |
|-----------|------|
| Wazuh Server | Central manager, indexer, dashboard, alert analysis, and integration point |
| Wazuh Agent | Windows endpoint telemetry collection and forwarding |
| pfSense | Firewall/router used to generate and forward network security logs |
| Suricata | IDS/IPS engine used to inspect traffic and generate EVE JSON alerts |
| VirusTotal | External threat-intelligence source used to enrich file and URL investigations |
| Kali Linux | Attacker machine used for the controlled Hydra SSH brute-force simulation |
| Windows endpoint | Monitored host for Wazuh agent, FIM, Sysmon, SSH events, and security logs |

## Tools and Technologies

- Wazuh SIEM/XDR
- Wazuh Agent
- pfSense
- Suricata IDS/IPS
- VirusTotal API
- Sysmon
- Windows Event Logs
- Hydra
- VMware
- PowerShell
- XML configuration snippets

## Key Outcomes

- Deployed and accessed the Wazuh dashboard from the lab network.
- Registered a Windows endpoint as an active Wazuh agent.
- Forwarded pfSense logs to Wazuh through syslog.
- Configured Suricata to produce EVE JSON and validated Suricata alerts in Wazuh.
- Integrated VirusTotal enrichment through Wazuh configuration.
- Configured Wazuh File Integrity Monitoring for a Windows folder and validated file-change alerts.
- Added Sysmon EventChannel ingestion for enhanced Windows telemetry.
- Simulated an SSH brute-force attack with Hydra and analyzed Wazuh/Windows failed-logon evidence.

## Skills Demonstrated

- SOC lab architecture design
- SIEM deployment and agent onboarding
- Firewall log forwarding
- IDS log ingestion
- Threat-intelligence enrichment
- File integrity monitoring
- Windows event and Sysmon telemetry
- Brute-force detection analysis
- Security alert validation and evidence-based reporting
