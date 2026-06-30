# SSH Brute Force Detection

This chapter simulates an SSH brute-force attack from Kali Linux against a Windows target with OpenSSH enabled. Wazuh is then used to detect and analyze the repeated failed logon activity.

---

## Purpose

The goal is to generate controlled authentication failures, validate that Wazuh detects them, and analyze the Windows Event 4625 fields behind the alert.

## Technical Context

A brute-force attack repeatedly tries username and password combinations until one succeeds. It is a simple but common technique for gaining unauthorized access to systems, applications, or accounts.

There are several related password-attack patterns. A simple brute-force attack tries many combinations directly. A dictionary attack uses a prepared list of likely passwords. A hybrid attack modifies dictionary words by adding numbers or symbols. A reverse brute-force attack starts with one common password and tests it against many usernames. Credential stuffing uses leaked username-password pairs from previous breaches.

Hydra is used in this lab to simulate the attack ethically inside a controlled environment. Hydra is a network login testing tool that can perform dictionary-based attacks against many protocols, including SSH, FTP, HTTP, RDP, Telnet, and others. Here it is used only to generate controlled SSH authentication failures for detection validation.

> Brute-force testing must only be performed against systems you own or have explicit permission to test. Outside a lab, unauthorized password attacks are illegal and harmful.

## Steps Covered

| Step | Description |
|------|-------------|
| Prepare password list | Create `passwords.txt` on Kali |
| Run Hydra | Attempt SSH logins against the lab target |
| Detect in Wazuh | Review failed logon and lockout alerts |
| Analyze Event 4625 | Interpret Windows failed-logon fields |

---

## Detailed Walkthrough

### Step 01 - Prepare the Password List

A small password list is created on Kali Linux. Hydra uses this list to test multiple passwords against the target SSH service.

> The password list controls the scope of the simulation. A small list is enough to validate detection without creating unnecessary noise. In this lab, the goal is not to break into the target; the goal is to create recognizable failed-login telemetry.

```bash
cat passwords.txt
```

![Screenshot 023 - Hydra Password List](../../images/08-ssh-brute-force-detection/023-hydra-password-list.png)

<p><sub><strong>Screenshot 023 - Hydra Password List:</strong> Kali shows the password list used for the controlled SSH brute-force simulation.</sub></p>

The screenshot confirms the input used by Hydra during the lab attack simulation.

---

### Step 02 - Run Hydra Against SSH

Hydra is launched with the known target username `dell` and the prepared password file. The target is the Windows system with OpenSSH Server enabled.

> SSH on Windows is not enabled by default. The test requires OpenSSH Server to be installed and running on the Windows target.

```bash
hydra -L <username_file> -P <password_file> <target>
hydra -l dell -P passwords.txt ssh://<target_ip>
```

The uppercase `-L` option is used when Hydra should read usernames from a file. The lowercase `-l` option is used when the username is already known and entered directly. The `-P` option points Hydra to the password list.

![Screenshot 024 - Hydra SSH Command](../../images/08-ssh-brute-force-detection/024-hydra-ssh-command.png)

<p><sub><strong>Screenshot 024 - Hydra SSH Command:</strong> Hydra runs from Kali against the SSH service using the `dell` username and the prepared password list.</sub></p>

Hydra creates multiple authentication attempts, which should appear as failed logons on the Windows target.

---

### Step 03 - Detect the Brute Force Activity in Wazuh

After the simulation, Wazuh Threat Hunting shows multiple failed SSH logons and an account lockout style alert. The visible rules include repeated `Logon Failure - Unknown user or bad password` events and a higher-severity lockout event.

> A single failed login can be normal. Multiple failures in a short time window from the same pattern become brute-force evidence.

![Screenshot 025 - Wazuh Brute Force Alerts](../../images/08-ssh-brute-force-detection/025-wazuh-brute-force-alerts.png)

<p><sub><strong>Screenshot 025 - Wazuh Brute Force Alerts:</strong> Wazuh shows repeated failed logon alerts for agent `MyPc`, including rule level 5 failed-logon events and a higher-level account-lockout event.</sub></p>

The screenshot confirms Wazuh detection of the authentication attack pattern.

---

### Step 04 - Analyze Windows Event 4625

Windows Event ID `4625` records failed logon attempts. In this lab, the event data shows SSH-related logon failures handled by `sshd.exe`, with the failure reason indicating an unknown username or bad password.

> Field-level analysis matters because the alert title alone does not explain the access method. The process name, logon type, status, substatus, and fired count show why this is SSH brute-force behavior instead of a single normal mistyped password.

| Field | Value | Meaning |
|-------|-------|---------|
| `data.win.system.eventID` | `4625` | Failed logon attempt |
| `data.win.eventdata.failureReason` | `%%2313` | Unknown user name or bad password |
| `data.win.eventdata.logonType` | `8` | NetworkCleartext logon type |
| `data.win.eventdata.status` | `0xc000006d` | Logon failure |
| `data.win.eventdata.subStatus` | `0xc0000064` | Unknown username or bad password condition |
| `data.win.eventdata.processName` | `C:\Windows\System32\OpenSSH\sshd.exe` | SSH service handled the attempt |
| `rule.firedtimes` | `14` | Similar failed logons occurred repeatedly |

The full field table is stored in [logs/windows-event-4625-field-analysis.md](../../logs/windows-event-4625-field-analysis.md).

The important conclusion is that the Windows endpoint `MyPc` received repeated SSH login attempts handled by `sshd.exe`. Wazuh parsed the Windows Security event through EventChannel, classified the activity as failed authentication, and showed that the same rule fired multiple times. That pattern is what makes the event suspicious in a SOC workflow.

---

## Validation

The lab confirms that Hydra generated repeated SSH authentication failures and that Wazuh detected the behavior through Windows Security events. Wazuh classified the failed authentication activity and surfaced repeated failures in Threat Hunting.

## Chapter Summary

The brute-force scenario validates the end-to-end detection path: attack simulation, Windows event generation, Wazuh ingestion, alerting, and field-level analysis.

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
