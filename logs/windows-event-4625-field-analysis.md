# Windows Event 4625 Field Analysis

| Field | Value | Meaning |
|-------|-------|---------|
| `data.win.eventdata.failureReason` | `%%2313` | Unknown user name or bad password. |
| `data.win.eventdata.keyLength` | `0` | No encryption key was used. |
| `data.win.eventdata.logonProcessName` | `Advapi` | Typical for service-based logon attempts rather than interactive desktop logons. |
| `data.win.eventdata.logonType` | `8` | NetworkCleartext logon type, commonly observed with services such as SSHD. |
| `data.win.eventdata.processId` | `0x65d8` | Process ID of the process that attempted the logon. |
| `data.win.eventdata.processName` | `C:\Windows\System32\OpenSSH\sshd.exe` | SSH service handled the attempted connection. |
| `data.win.eventdata.status` | `0xc000006d` | Logon failure. |
| `data.win.eventdata.subStatus` | `0xc0000064` | Unknown user name or bad password. |
| `data.win.system.channel` | `Security` | Event came from the Windows Security log. |
| `data.win.system.eventID` | `4625` | Failed logon attempt. |
| `data.win.system.systemTime` | `2025-08-05T20:51:34.3997254Z` | UTC timestamp for the failed logon. |
| `decoder.name` | `windows_eventchannel` | Wazuh parsed the event as a Windows EventChannel log. |
| `rule.firedtimes` | `14` | At least fourteen similar failed-login events occurred. |
