# pfSense Log Integration

This chapter adds firewall visibility to the SOC lab by forwarding pfSense logs to Wazuh. pfSense acts as the network gateway, while Wazuh collects and analyzes the firewall events.

---

## Purpose

The goal is to deploy pfSense in VMware, assign WAN and LAN interfaces, access the web interface, enable remote syslog, and prepare Wazuh to receive and classify pfSense events.

## Technical Context

A firewall inspects and controls incoming and outgoing traffic according to predefined rules. In a simple analogy, it works like a security checkpoint: traffic that matches allowed policy can pass, while unwanted or suspicious traffic can be denied, logged, or inspected more deeply.

Firewalls help prevent unauthorized access, block malicious traffic, and segment networks so internal systems are not exposed unnecessarily. Common firewall approaches include packet filtering, stateful inspection, proxy-based filtering, and next-generation firewall features. Each adds a different level of context, from basic IP/port decisions to application-aware and threat-aware inspection.

pfSense is used in this lab as the network gateway and firewall. It can generate logs for allowed traffic, blocked traffic, authentication events, DHCP, DNS, VPN activity, and system events. Forwarding those logs to Wazuh gives the SOC analyst a central view of network behavior and allows firewall events to be reviewed together with endpoint and IDS telemetry.

Combining pfSense with Wazuh creates a small SOC-style workflow. pfSense observes the traffic path at the network edge, while Wazuh collects and analyzes the logs. This makes it possible to compare network events with endpoint events instead of treating them as separate, disconnected records.

## Steps Covered

| Step | Description |
|------|-------------|
| Build pfSense VM | Assign WAN and LAN interfaces |
| Access web UI | Login and verify dashboard |
| Forward logs | Enable remote syslog to Wazuh |
| Parse logs | Add Wazuh syslog input, decoder, and custom rules |

---

## Detailed Walkthrough

### Step 01 - Build the pfSense Firewall VM

The pfSense VM is created in VMware with two network adapters. Adapter 1 is used as WAN through NAT, and Adapter 2 is used as the isolated LAN side for the lab.

> Interface direction matters in firewall projects. WAN represents the outside-facing side, while LAN represents the internal lab network that should be protected and monitored. If WAN and LAN are mixed up, the firewall can expose the wrong side of the lab or fail to route traffic correctly.

![Screenshot 010 - pfSense Interface Assignment](../../images/03-pfsense-log-integration/010-pfsense-interface-assignment.png)

<p><sub><strong>Screenshot 010 - pfSense Interface Assignment:</strong> pfSense console shows interface assignment and the LAN management address, confirming the firewall has a reachable internal interface.</sub></p>

The console confirms that pfSense has a LAN address and can be managed from the lab side.

---

### Step 02 - Access the pfSense Web Interface

A browser from a LAN-connected VM is used to access `https://192.168.1.1/`. The default credentials are used for initial setup, and the default password should be changed immediately after first login.

> Default firewall credentials are acceptable only during isolated lab setup. In real environments, leaving default credentials active is a serious exposure.

```text
URL: https://192.168.1.1/
Username: admin
Password: pfsense
```

![Screenshot 011 - pfSense Web Login](../../images/03-pfsense-log-integration/011-pfsense-web-login.png)

<p><sub><strong>Screenshot 011 - pfSense Web Login:</strong> The pfSense login page is reachable from the LAN side, confirming browser access to the firewall interface.</sub></p>

![Screenshot 012 - pfSense Dashboard](../../images/03-pfsense-log-integration/012-pfsense-dashboard.png)

<p><sub><strong>Screenshot 012 - pfSense Dashboard:</strong> The pfSense dashboard confirms that the firewall is running and accessible for configuration.</sub></p>

The dashboard validates that the firewall is operational before log forwarding is configured.

---

### Step 03 - Enable pfSense Remote Logging

Remote logging is enabled under `Status -> System Logs -> Settings`. The Wazuh server IP is added as the remote syslog destination on UDP port `514`, and relevant categories such as system, firewall, VPN, DHCP, and DNS logs are selected.

> Syslog forwarding allows network gateway events to leave pfSense and reach the SIEM. Without this step, Wazuh cannot correlate firewall activity with endpoint or IDS telemetry.

```text
Remote syslog server: <WAZUH_SERVER_IP>
Syslog port: 514/UDP
Forwarded categories: System, Firewall, VPN, DHCP, DNS
```

![Screenshot 013 - pfSense Remote Logging](../../images/03-pfsense-log-integration/013-pfsense-remote-logging.png)

<p><sub><strong>Screenshot 013 - pfSense Remote Logging:</strong> pfSense remote logging settings show the Wazuh server configured as the syslog destination.</sub></p>

The screenshot confirms that pfSense is configured to send selected log categories to the Wazuh server.

---

### Step 04 - Configure Wazuh Syslog Input and pfSense Parsing

Wazuh must listen for syslog on UDP port `514`, and custom decoder/rule logic can be added to identify pfSense firewall events. The decoder extracts fields such as source IP, destination IP, protocol, and ports, while rules assign alert levels for allowed traffic, blocked traffic, successful logins, and authentication errors.

> Syslog is the transport path for sending firewall events to the SIEM. A syslog listener receives the raw message, but decoders and rules give it security meaning. Parsing turns text into fields that analysts can search, filter, and alert on.

```xml
<remote>
  <connection>syslog</connection>
  <port>514</port>
  <protocol>udp</protocol>
  <allowed-ips>YOUR_PFSENSE_IP</allowed-ips>
  <local_ip>YOUR_WAZUH_SERVER_IP</local_ip>
</remote>
```

```xml
<rule id="100114" level="7">
  <if_sid>100111</if_sid>
  <match>block</match>
  <description>pfSense: Blocked traffic from $(srcip) to $(dstip)</description>
</rule>
```

The custom rule levels give the firewall events different investigation weight. Informational events such as successful logins can be lower severity, allowed traffic can be normal visibility, blocked traffic can be treated as warning-level evidence, and authentication errors can be raised higher because they may indicate attempted unauthorized access.

Full snippets are stored in [configs/pfsense-syslog-input.xml](../../configs/pfsense-syslog-input.xml), [configs/pfsense-custom-decoder.xml](../../configs/pfsense-custom-decoder.xml), and [configs/pfsense-custom-rules.xml](../../configs/pfsense-custom-rules.xml).

The configuration partially validates pfSense integration by defining the forwarding path and parsing logic. A production deployment would also validate live received events in Wazuh and test each custom rule against real pfSense `filterlog` samples.

---

## Validation

pfSense is reachable, remote logging is configured, and Wazuh has syslog input and parsing snippets prepared. The evidence confirms configuration; live log receipt and decoder matching should be validated with actual pfSense events.

## Chapter Summary

pfSense extends the lab with firewall telemetry. The next chapter adds Suricata IDS visibility, which gives Wazuh more detailed network threat-detection events.

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
