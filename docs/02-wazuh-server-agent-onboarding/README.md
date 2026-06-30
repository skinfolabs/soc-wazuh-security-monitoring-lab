# Wazuh Server and Agent Onboarding

This chapter combines Wazuh server deployment with Windows agent onboarding. The Wazuh server provides the manager, indexer, and dashboard, while the Windows agent collects endpoint telemetry and forwards it to the manager.

---

## Purpose

The goal is to bring the Wazuh platform online, recover from a dashboard readiness issue when services are not fully started, and enroll a Windows endpoint as an active monitored agent.

## Technical Context

Wazuh provides an OVA virtual appliance that can be imported into VMware. An OVA is useful in a lab because it packages the Wazuh manager, indexer, and dashboard into a ready virtual machine instead of requiring each component to be installed manually. After booting the VM, the server IP is used to reach the dashboard over HTTPS.

The Wazuh server is the central analysis side of the lab. The manager receives events, decodes them, applies detection rules, and generates alerts. The indexer stores the data for search and visualization. The dashboard is the analyst interface used to review agents, alerts, modules, and threat-hunting results.

The Windows endpoint is then enrolled with the Wazuh agent. The agent is a lightweight application installed on systems such as Windows, Linux, and macOS. It collects security telemetry and forwards it to the manager. Its modules can support log collection, File Integrity Monitoring, Security Configuration Assessment, and malware-related checks. In this lab, the same Windows agent becomes important later for FIM, Sysmon, Suricata log ingestion, and failed-logon visibility.

## Steps Covered

| Step | Description |
|------|-------------|
| Import and access Wazuh | Start the OVA and open the dashboard |
| Troubleshoot services | Start Wazuh manager, dashboard, and indexer services |
| Deploy Windows agent | Generate and run endpoint enrollment commands |

---

## Detailed Walkthrough

### Step 01 - Import the Wazuh OVA and Find the Server IP

The Wazuh OVA is imported into VMware and started as the central SIEM virtual machine. After boot, the server IP is identified from the terminal so the dashboard can be reached through a browser.

> The Wazuh server is the central collection and analysis point. If its IP address is wrong or unreachable, no endpoint onboarding or dashboard validation can happen. The dashboard may be the visible interface, but it depends on the manager and indexer services behind it.

```bash
ifconfig
```

![Screenshot 002 - Wazuh Login Page](../../images/02-wazuh-server-agent-onboarding/002-wazuh-login-page.png)

<p><sub><strong>Screenshot 002 - Wazuh Login Page:</strong> The Wazuh login page is reachable over HTTPS, confirming that the browser can reach the Wazuh dashboard service.</sub></p>

The login page confirms network reachability to the Wazuh server. The lab uses the default appliance credentials only for initial access and setup.

---

### Step 02 - Recover the Dashboard When Services Are Not Ready

The dashboard can show a server-not-ready message when Wazuh services are not fully running. The manager, dashboard, and indexer services are checked and started from the appliance terminal.

> The dashboard depends on backend services. The web page may load while the application is still unable to query the manager or indexer, so service status must be validated on the server.

```bash
sudo su
systemctl status wazuh-manager
systemctl start wazuh-manager
systemctl status wazuh-dashboard
systemctl start wazuh-dashboard
systemctl status wazuh-indexer
systemctl start wazuh-indexer
```

![Screenshot 003 - Dashboard Server Not Ready](../../images/02-wazuh-server-agent-onboarding/003-dashboard-server-not-ready.png)

<p><sub><strong>Screenshot 003 - Dashboard Server Not Ready:</strong> The browser shows that Wazuh is reachable but the dashboard backend is not ready, which points to a service readiness issue rather than a network outage.</sub></p>

![Screenshot 004 - Main Wazuh Dashboard](../../images/02-wazuh-server-agent-onboarding/004-main-wazuh-dashboard.png)

<p><sub><strong>Screenshot 004 - Main Wazuh Dashboard:</strong> The Wazuh dashboard loads after the required services are running, confirming successful access to the SIEM interface.</sub></p>

The evidence confirms that the service issue was resolved and the dashboard became usable for agent deployment and later investigation steps.

---

### Step 03 - Generate the Windows Agent Deployment

The Wazuh dashboard is used to deploy a new agent. Windows is selected as the target package, the Wazuh manager address is entered, and a unique agent name such as `PC1` is assigned.

> The agent is the endpoint sensor. Choosing the correct operating system package matters because Windows, Linux, and macOS agents are installed differently. Agent names make endpoint telemetry easier to identify in alerts, dashboards, and threat-hunting views.

![Screenshot 005 - Windows Agent Package Selection](../../images/02-wazuh-server-agent-onboarding/005-windows-agent-package-selection.png)

<p><sub><strong>Screenshot 005 - Windows Agent Package Selection:</strong> The deployment workflow is set to Windows, matching the endpoint that will forward logs to Wazuh.</sub></p>

![Screenshot 006 - Agent Server Address and Name](../../images/02-wazuh-server-agent-onboarding/006-agent-server-address-and-name.png)

<p><sub><strong>Screenshot 006 - Agent Server Address and Name:</strong> The Wazuh manager address and endpoint name are entered before the dashboard generates registration commands.</sub></p>

The server address binds the endpoint to the correct manager. The agent name becomes the endpoint identity visible in Wazuh.

---

### Step 04 - Register and Start the Windows Agent

Wazuh generates the Windows enrollment commands after the platform, manager address, and agent name are configured. The generated commands are run in PowerShell as Administrator so the endpoint can install, register, and start the agent service.

> The registration command is the bridge between the endpoint and the manager. Installation alone is not enough; the agent must also know where to send telemetry and must start its Windows service.

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.11.2-1.msi -OutFile $env:tmp\wazuh-agent
msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER="<WAZUH_MANAGER_IP>"
NET START WazuhSvc
```

![Screenshot 007 - Generated Agent Commands](../../images/02-wazuh-server-agent-onboarding/007-generated-agent-commands.png)

<p><sub><strong>Screenshot 007 - Generated Agent Commands:</strong> The dashboard provides the command sequence needed to install and enroll the Windows agent.</sub></p>

![Screenshot 008 - PowerShell Agent Registration](../../images/02-wazuh-server-agent-onboarding/008-agent-registration-powershell.png)

<p><sub><strong>Screenshot 008 - PowerShell Agent Registration:</strong> PowerShell runs the generated Wazuh agent commands with administrator privileges on the Windows endpoint.</sub></p>

![Screenshot 009 - Active Wazuh Agent](../../images/02-wazuh-server-agent-onboarding/009-agent-active-dashboard.png)

<p><sub><strong>Screenshot 009 - Active Wazuh Agent:</strong> The Windows endpoint appears as an active agent, confirming successful enrollment and communication with the Wazuh server.</sub></p>

The active status validates that the endpoint is registered and sending data to Wazuh. This is the foundation for later FIM, Sysmon, and brute-force detection work.

After installation, the Windows agent files are stored under `C:\Program Files (x86)\ossec-agent`. This location is used later when editing `ossec.conf` for File Integrity Monitoring, VirusTotal-triggering paths, Suricata log collection, and Sysmon EventChannel collection.

---

## Validation

The Wazuh dashboard loads successfully, the service-readiness issue is resolved, and the Windows endpoint appears as an active Wazuh agent. This confirms server availability and endpoint communication.

## Chapter Summary

Wazuh is operational and the Windows endpoint is enrolled. The next chapter adds pfSense firewall telemetry so Wazuh can receive network gateway events in addition to endpoint data.

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
