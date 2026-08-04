# Investigation 001 – Endpoint Intrusion

## Overview

This investigation simulates a phishing attack against a Windows endpoint in a controlled lab environment. The objective is to demonstrate how an attacker progresses through the early stages of the Cyber Kill Chain and how a SOC analyst can investigate the generated telemetry using Splunk Enterprise, Sysmon, and Windows Firewall logs.

> **Disclaimer**
>
> This investigation was conducted in an isolated virtual lab for educational purposes only. The PowerShell payload is non-destructive and performs only read-only system discovery to generate realistic security telemetry.

---

# Lab Environment

| Component | Technology |
|-----------|------------|
| Attacker Machine | Kali Linux |
| Victim Machine | Windows 10 |
| SIEM | Splunk Enterprise |
| Endpoint Monitoring | Sysmon |
| Log Collection | Splunk Universal Forwarder |
| Network | VMware NAT |

---

# Attack Scenario

An attacker performs reconnaissance against a Windows workstation to identify exposed services and determine the operating system. After confirming the target is a Windows endpoint, the attacker prepares a PowerShell script disguised as an internal IT diagnostic tool.

The payload is delivered through a phishing email hosted on a Python HTTP server running on the attacker's machine. The objective of the simulation is to generate realistic Windows telemetry that can be investigated from a Security Operations Center (SOC) perspective.

---

# Cyber Kill Chain

| Phase | Status |
|--------|--------|
| Reconnaissance | ✅ Completed |
| Weaponization | ✅ Completed |
| Delivery | ✅ Completed |
| Execution | 🚧 In Progress |
| Installation | Planned |
| Command & Control | Planned |
| Actions on Objectives | Planned |

---

# Phase 1 – Reconnaissance

## Objective

Identify the target operating system and enumerate exposed network services.

## Attacker Activity

The attacker performed an Nmap SYN scan against the Windows endpoint.

```bash
nmap -sS -sV <VICTIM_IP>
```

## Findings

The scan identified the following services:

- TCP 135 (RPC)
- TCP 139 (NetBIOS)
- TCP 445 (SMB)

These services confirmed that the target was a Windows workstation and exposed common Microsoft networking services.

### Detection Query

```spl
index=endpoint
sourcetype=windows:firewall
RECEIVE
(" 135 " OR " 139 " OR " 445 ")
| table _time _raw
```

### Evidence

**Figure 1.** Nmap scan identifying the target's exposed services.

<p align="center">
<img src="screenshots/Image1.png" width="900">
</p>

---

**Figure 2.** Splunk investigation showing Windows Firewall events generated during the reconnaissance phase.

<p align="center">
<img src="screenshots/Image2.png" width="900">
</p>

---

# Phase 2 – Weaponization

## Objective

Prepare a payload capable of generating realistic endpoint telemetry.

## Attacker Activity

The attacker created a PowerShell script named **IT_Diagnostic.ps1** disguised as an internal IT diagnostic utility.

The payload safely performs:

- User discovery
- Host discovery
- Network enumeration
- Running process enumeration
- Antivirus discovery

No destructive or unauthorized actions are performed.

### Evidence

**Figure 3.** PowerShell payload used during the simulation.

<p align="center">
<img src="screenshots/Image3.png" width="900">
</p>

---

# Phase 3 – Delivery

## Objective

Deliver the payload through a phishing email.

## Attacker Activity

The payload was hosted on a Python HTTP server running on the Kali Linux attacker machine.

```bash
python3 -m http.server 8000
```

A phishing email impersonating the organization's IT Support team instructed the victim to download the PowerShell diagnostic script.

Download URL:

```
http://<ATTACKER_IP>:8000/IT_Diagnostic.ps1
```

## Findings

The Python HTTP server recorded the victim's request for the payload, confirming successful delivery.

### Evidence

**Figure 4.** Simulated phishing email used to deliver the payload.

<p align="center">
<img src="screenshots/Image4.png" width="900">
</p>

---

**Figure 5.** Python HTTP server hosting the PowerShell payload.

<p align="center">
<img src="screenshots/Image5.png" width="900">
</p>

---

**Figure 6.** Python HTTP server log confirming successful payload download.

<p align="center">
<img src="screenshots/Image6.png" width="900">
</p>

---

# MITRE ATT&CK Mapping

| Phase | Technique | ID |
|--------|-----------|----|
| Reconnaissance | Active Scanning | T1595 |
| Weaponization | PowerShell | T1059.001 |
| Weaponization | System Information Discovery | T1082 |
| Weaponization | Software Discovery | T1518 |
| Delivery | Spearphishing Link | T1566.002 |

---

# Tools Used

- Kali Linux
- Nmap
- Python HTTP Server
- Windows 10
- Sysmon
- Splunk Universal Forwarder
- Splunk Enterprise

---

# Skills Demonstrated

- Network Reconnaissance
- Windows Endpoint Investigation
- Splunk SPL
- Windows Firewall Log Analysis
- Sysmon Log Analysis
- PowerShell Investigation
- Phishing Analysis
- MITRE ATT&CK Mapping
- Cyber Kill Chain Analysis

---

# Repository Structure

```
001-Endpoint-Intrusion/
│
├── README.md
├── screenshots/
│   ├── Image1.png
│   ├── Image2.png
│   ├── Image3.png
│   ├── Image4.png
│   ├── Image5.png
│   └── Image6.png
│
├── payload/
│   └── IT_Diagnostic.ps1
│
├── email/
│   └── phishing_email.html
│
└── queries/
    ├── recon.spl
    ├── execution.spl
    ├── persistence.spl
    └── c2.spl
```

---

# Next Steps

The remaining phases of the Cyber Kill Chain will be documented as the investigation progresses:

- Execution
- Installation (Persistence)
- Command & Control
- Actions on Objectives
- Final Timeline
- Indicators of Compromise (IOCs)
- Detection Recommendations
