# MSMQ Vulnerability (CVE-2023-21554)

Payload for Exploit is adapted from 3tternp at https://github.com/3tternp/CVE-2023-21554

This repository contains documentation, detection techniques, and mitigation strategies for the Microsoft Message Queuing (MSMQ) Remote Code Execution vulnerability — CVE-2023-21554, also known as "QueueJumper".

## 📌 Overview

- **Vulnerability**: Microsoft Message Queuing (MSMQ) Remote Code Execution
- **CVE ID**: CVE-2023-21554
- **CVSS Score**: 9.8 (Critical)
- **Affected Port**: TCP 1801
- **Impact**: Unauthenticated remote code execution via specially crafted MSMQ messages
- **Affected Systems**: Windows 10/11, Windows Server 2016/2019/2022 (when MSMQ is enabled)

## 📖 Description

CVE-2023-21554 is a critical vulnerability in the MSMQ service, which can be exploited by sending malformed packets to TCP port 1801. This allows attackers to execute arbitrary code without authentication, leading to full system compromise.

The vulnerability is due to improper input validation in the handling of section headers within MSMQ messages. Exploiting this flaw can result in memory corruption and remote code execution within the `mqsvc.exe` process.

---

## 🔎 Detection Techniques

### ✅ Network-Based Detection
- Monitor TCP port 1801 using Wireshark filters:
  - `tcp.port == 1801`
  - `tcp.len > 1024` (suspicious payloads)
- Detect retransmissions or malformed packets:
  - `tcp.analysis.retransmission`

### ✅ Host-Based Detection
- Use PowerShell to scan for `mqsvc.exe` crashes:
  ```powershell
  Get-EventLog -LogName Application -Source "Application Error" | Where-Object { $_.Message -like "*mqsvc.exe*" }
Use Sysmon to track suspicious process executions or crashes:

Monitor mqsvc.exe and its child processes (powershell.exe, cmd.exe)

✅ Log Analysis
Use Windows Event Viewer:

Look for Event ID 2063 or 1000 (application errors)

Splunk Query:
index=windows EventCode=2063 OR EventCode=1000 | table _time, host, EventCode, Message

🛡️ Mitigation Techniques
🔒 Network Isolation
Block TCP port 1801 from untrusted networks:
New-NetFirewallRule -DisplayName "Block MSMQ" -Direction Inbound -Protocol TCP -LocalPort 1801 -Action Block

🧩 Least Privilege
Run MSMQ under a low-privileged service account

Remove write access to system directories for MSMQ

🔍 Runtime Monitoring
Enable ETW and exploit protection:
Set-ProcessMitigation -Name mqsvc.exe -Enable CFG -Enable DEP -Enable HeapTermination

📁 Repository Contents
File	Description
README.md	Project overview and summary
exploit-notes.md	Detailed step-by-step detection and mitigation guide
poc.py or lab-results.txt	(If included) Proof-of-concept or analysis logs
/screenshots	Packet captures, error logs, or detection screenshots
references.txt	Full list of external sources and citations

📚 References
CISA Alert: CVE-2023-21554

Microsoft Security Update Guide

Wireshark MSMQ Traffic Filters

NVD Entry – CVE-2023-21554

🛑 Disclaimer: This project is for academic and ethical research purposes only. Do not attempt exploitation on unauthorized systems.


