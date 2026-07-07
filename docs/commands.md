# Commands and Configuration Reference

This file collects the main commands and configuration snippets used in the lab. The same commands are also shown next to the relevant walkthrough steps.

## Wazuh Server

```bash
ifconfig
```

```bash
sudo su
systemctl status wazuh-manager
systemctl start wazuh-manager
systemctl status wazuh-dashboard
systemctl start wazuh-dashboard
systemctl status wazuh-indexer
systemctl start wazuh-indexer
```

## Wazuh Windows Agent

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.11.2-1.msi -OutFile $env:tmp\wazuh-agent
msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER="<WAZUH_MANAGER_IP>"
NET START WazuhSvc
```

## pfSense Syslog Input

```xml
<remote>
  <connection>syslog</connection>
  <port>514</port>
  <protocol>udp</protocol>
  <allowed-ips>YOUR_PFSENSE_IP</allowed-ips>
  <local_ip>YOUR_WAZUH_SERVER_IP</local_ip>
</remote>
```

## Suricata

```powershell
Get-Service -Name npcap
Start-Service -Name npcap
Get-NetAdapter | Select Name, Status
cd "C:\Program Files\Suricata\"
suricata -c suricata.yaml -i <interface>
Restart-Service -Name WazuhSvc
ping google.com
```

## VirusTotal

```bash
sudo nano /var/ossec/etc/ossec.conf
sudo systemctl restart wazuh-manager
```

## File Integrity Monitoring

```powershell
notepad "C:\Program Files (x86)\ossec-agent\ossec.conf"
Restart-Service -Name WazuhSvc
```

## Sysmon

```powershell
sysmon.exe -i -accepteula
sysmon -c sysmonconfig.xml
Restart-Service -Name WazuhSvc
```

## SSH Brute Force Simulation

```bash
hydra -L <username_file> -P <password_file> <target>
hydra -l dell -P passwords.txt ssh://<target_ip>
```
