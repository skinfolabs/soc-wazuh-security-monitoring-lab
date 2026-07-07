# Topology and Lab Environment

This chapter explains the SOC lab architecture before the technical integrations begin. The environment uses Wazuh as the central SIEM/XDR platform, pfSense as the firewall, Suricata as the IDS sensor, VirusTotal as an enrichment source, a Windows endpoint as the monitored host, and Kali Linux as the controlled attacker machine.

## Technical Context

A SIEM collects, normalizes, stores, and analyzes security data from endpoints, firewalls, IDS sensors, applications, and authentication systems. Wazuh is used here because it combines agent telemetry, alerting, file integrity monitoring, dashboard visibility, and integrations such as VirusTotal in one open-source platform.

Wazuh is built around several roles. The agent collects local endpoint telemetry. The manager receives events, decodes logs, applies rules, and generates alerts. The indexer stores searchable event data. The dashboard provides the analyst interface for reviewing agents, alerts, modules, and threat-hunting results.

> A useful SOC lab needs more than one log source. Endpoint, firewall, IDS, and authentication data provide different views of the same environment and help separate isolated alerts from meaningful attack patterns.

**Implemented controls:**

- Defined the lab architecture and telemetry flow.
- Identified the Wazuh server, Windows endpoint, pfSense firewall, Suricata sensor, VirusTotal enrichment, and Kali attacker roles.
- Established the relationship between endpoint telemetry, network logs, IDS alerts, and authentication-failure evidence.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| SIEM | Central platform for collecting, searching, correlating, and alerting on security events. |
| Wazuh Agent | Endpoint sensor that forwards local telemetry to the Wazuh manager. |
| pfSense | Firewall/router generating network and gateway logs. |
| Suricata | IDS/IPS engine that inspects traffic and writes structured alert logs. |
| VirusTotal | External reputation source used to enrich file and indicator investigations. |
| Telemetry flow | The path security data takes from sensors and endpoints into Wazuh. |

---

## Topology

The diagram shows the main monitoring path: Kali generates controlled activity, traffic passes through pfSense, Suricata inspects network events, Wazuh collects endpoint and security-tool telemetry, and VirusTotal provides reputation enrichment.

![Wazuh SOC Lab Architecture](../../images/01-topology-and-lab-environment/001-wazuh-soc-lab-architecture.png)

<p><sub><strong>Screenshot 001 - Wazuh SOC Lab Architecture:</strong> The topology shows the attacker path, internet route, pfSense firewall, Suricata IDS/IPS sensor, Wazuh SIEM/XDR server, VirusTotal enrichment, and Windows Wazuh agent.</sub></p>

This confirms the intended security-monitoring model: network activity is controlled and logged at the firewall, inspected by Suricata, enriched where needed, and reviewed centrally in Wazuh.

---

## Lab Environment

| Component | Role |
|-----------|------|
| Wazuh Server | Central manager, indexer, dashboard, alert analysis, and integration point. |
| Wazuh Agent | Windows endpoint telemetry collection and forwarding. |
| pfSense | Firewall/router used to generate and forward network security logs. |
| Suricata | IDS/IPS sensor used to inspect traffic and generate EVE JSON alerts. |
| VirusTotal | External threat-intelligence source for file and indicator enrichment. |
| Kali Linux | Attacker machine used for the controlled Hydra SSH brute-force simulation. |
| Windows endpoint | Monitored host for Wazuh agent, FIM, Sysmon, SSH events, and security logs. |

## Validation and Summary

The topology establishes how the later chapters fit together: Wazuh receives endpoint data from the agent, firewall logs from pfSense, IDS alerts from Suricata, reputation context from VirusTotal, file-change telemetry from FIM, Sysmon events, and authentication-failure evidence from the Windows endpoint.

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
