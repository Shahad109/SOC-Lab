# Investigation 001 – Phishing Attack

## Overview

This investigation simulates a phishing attack against a Windows endpoint in a controlled lab environment. The objective is to demonstrate how an attacker progresses through the early stages of the Cyber Kill Chain and how a SOC analyst can identify and investigate the associated telemetry using Splunk Enterprise, Sysmon, and Windows Firewall logs.

> **Disclaimer**
>
> This project is intended for educational purposes only. The PowerShell payload is non-malicious and performs only read-only system discovery to generate realistic telemetry for analysis.

---

# Phase 1 – Reconnaissance

## Objective

Identify the target operating system and enumerate exposed services before launching the attack.

### Attacker Action

The attacker performed an Nmap SYN scan against the Windows endpoint.

```bash
nmap -sS -sV <VICTIM_IP>
```

### Findings

The scan identified the following services:

| Port | Service |
|------|---------|
| 135 | Microsoft RPC |
| 139 | NetBIOS |
| 445 | SMB |

These services confirmed that the target was a Windows workstation and helped the attacker prepare a Windows-specific attack.

### Evidence

- Nmap scan results
- Windows Firewall logs
- Splunk search results

### Detection Query

```spl
index=endpoint
sourcetype=windows:firewall
(" 135 " OR " 139 " OR " 445 ")
| table _time _raw
```

### MITRE ATT&CK

- **T1595 – Active Scanning**

---

# Phase 2 – Weaponization

## Objective

Prepare a payload that appears legitimate while generating realistic telemetry for investigation.

### Attacker Action

The attacker created a PowerShell script named **IT_Diagnostic.ps1** disguised as an internal IT diagnostic tool.

The script safely collects:

- Current user
- Computer name
- Network configuration
- Running processes
- Installed antivirus information

No destructive or malicious actions are performed.

### Payload

```
IT_Diagnostic.ps1
```

### Evidence

- PowerShell payload
- Source code

### MITRE ATT&CK

- **T1059.001 – PowerShell**
- **T1082 – System Information Discovery**
- **T1518 – Software Discovery**

---

# Phase 3 – Delivery

## Objective

Deliver the payload to the victim using a phishing email.

### Attacker Action

The attacker hosted the payload on a Python HTTP server.

```bash
python3 -m http.server 8000
```

A phishing email impersonating the organization's IT Support team instructed the victim to download the diagnostic script.

```
http://<ATTACKER_IP>:8000/IT_Diagnostic.ps1
```

### Evidence

- HTML phishing email
- Python HTTP server log
- Victim download request

Example:

```
GET /IT_Diagnostic.ps1 HTTP/1.1 200
```

### MITRE ATT&CK

- **T1566.002 – Spearphishing Link**

---

# Investigation Summary

The attacker successfully completed the first three phases of the Cyber Kill Chain by:

- Performing reconnaissance against the Windows endpoint.
- Preparing a PowerShell-based payload disguised as an IT diagnostic tool.
- Delivering the payload through a phishing email hosted on a Python web server.

These actions generated network and endpoint telemetry that can be analyzed using Splunk Enterprise and Sysmon.
