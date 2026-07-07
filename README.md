# Wazuh Complete SOC Lab

![Category](https://img.shields.io/badge/Category-SOC%20Lab-blue)
![Platform](https://img.shields.io/badge/Platform-Wazuh%20%7C%20pfSense%20%7C%20Suricata-lightgrey)
![Focus](https://img.shields.io/badge/Focus-SIEM%20%7C%20XDR%20%7C%20Detection-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Project Overview

This project documents a complete SOC home lab built around Wazuh SIEM/XDR. The lab combines a Wazuh server, a Windows endpoint agent, pfSense firewall logging, Suricata IDS alerts, VirusTotal enrichment, File Integrity Monitoring, Sysmon telemetry, and a controlled SSH brute-force detection scenario.

The project demonstrates how endpoint, firewall, IDS, threat-intelligence, file-change, Sysmon, and authentication-failure data can be collected into one monitoring workflow. The evidence shows dashboard access, active agent enrollment, log-forwarding configuration, IDS event ingestion, FIM alerts, Sysmon collection setup, and Wazuh detection of repeated SSH failures.

## Objectives

- Deploy Wazuh as the central SIEM/XDR platform for the lab.
- Enroll a Windows endpoint as an active monitored agent.
- Forward pfSense firewall logs and Suricata IDS events into Wazuh.
- Configure VirusTotal enrichment, File Integrity Monitoring, and Sysmon collection.
- Simulate SSH brute-force activity with Hydra and analyze the resulting Wazuh and Windows Event 4625 evidence.

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 1 | [Topology and Lab Environment](docs/01-topology-and-lab-environment/README.md) | Lab architecture, component roles, telemetry flow, and trust boundaries |
| 2 | [Wazuh Server and Agent Onboarding](docs/02-wazuh-server-agent-onboarding/README.md) | Wazuh OVA access, service recovery, and Windows agent registration |
| 3 | [pfSense Log Integration](docs/03-pfsense-log-integration/README.md) | Firewall setup, remote syslog forwarding, and Wazuh decoder/rule logic |
| 4 | [Suricata IDS Integration](docs/04-suricata-ids-integration/README.md) | Suricata EVE JSON logging, Wazuh ingestion, and alert validation |
| 5 | [VirusTotal Threat Intelligence](docs/05-virustotal-threat-intelligence/README.md) | API key handling, Wazuh manager integration, and monitored directory enrichment |
| 6 | [File Integrity Monitoring](docs/06-file-integrity-monitoring/README.md) | Windows FIM configuration and file create/modify/delete alert validation |
| 7 | [Sysmon Log Ingestion](docs/07-sysmon-log-ingestion/README.md) | Windows Event Log concepts, Sysmon installation, and EventChannel ingestion |
| 8 | [SSH Brute Force Detection](docs/08-ssh-brute-force-detection/README.md) | Hydra simulation, Wazuh detection, Windows Event 4625 analysis, and defensive controls |
| 9 | [Final Summary](docs/09-final-summary/README.md) | Validation summary, production recommendations, and skills demonstrated |

## Tools and Technologies

- Wazuh SIEM/XDR and Wazuh Agent
- pfSense firewall and remote syslog
- Suricata IDS/IPS with EVE JSON
- VirusTotal API enrichment
- Windows Event Logs, Sysmon, and EventChannel collection
- File Integrity Monitoring with Wazuh syscheck
- Kali Linux, Hydra, VMware, PowerShell, XML, and YAML

## Skills Demonstrated

- SOC lab architecture and telemetry planning
- SIEM deployment and endpoint onboarding
- Firewall and IDS log ingestion
- Threat-intelligence enrichment
- File integrity and Windows telemetry monitoring
- SSH brute-force simulation and Event 4625 analysis
- Evidence-based validation and defensive recommendation writing
