# Investigation 001 – Phishing Attack

## Phase 1 – Reconnaissance

### Objective

Identify the target operating system and enumerate exposed network services.

### Attacker Activity

The attacker performed an Nmap scan against the Windows endpoint.

```bash
nmap -sS -sV <VICTIM_IP>
```

### Findings

The scan identified several Microsoft networking services, including RPC (135), NetBIOS (139), and SMB (445). These services confirmed that the target was a Windows workstation and provided valuable information for planning the next stage of the attack.

---

**Figure 1.** Nmap scan results showing the open services discovered on the Windows endpoint.

<p align="center">
<img src="screenshots/recon/nmap_scan.png" width="800">
</p>

---

Windows Firewall logs confirmed multiple inbound connection attempts targeting ports **135**, **139**, and **445**, indicating reconnaissance activity from the attacker.

### Splunk Detection Query

```spl
index=endpoint
sourcetype=windows:firewall
RECEIVE
(" 135 " OR " 139 " OR " 445 ")
| table _time _raw
```

---

**Figure 2.** Splunk investigation showing Windows Firewall events generated during the reconnaissance phase.

<p align="center">
<img src="screenshots/recon/splunk_recon.png" width="900">
</p>

---

## Phase 2 – Weaponization

### Objective

Prepare a payload capable of generating realistic endpoint telemetry.

### Attacker Activity

The attacker created a PowerShell script named **IT_Diagnostic.ps1**, disguised as an internal IT diagnostic tool.

The script safely performs:

- User discovery
- Host discovery
- Network enumeration
- Running process enumeration
- Antivirus discovery

No destructive actions are performed.

---

**Figure 3.** PowerShell payload used during the simulation.

<p align="center">
<img src="screenshots/weaponization/payload.png" width="900">
</p>

---

## Phase 3 – Delivery

### Objective

Deliver the payload through a phishing email.

### Attacker Activity

The payload was hosted on a Python HTTP server running on the Kali Linux attacker machine.

```bash
python3 -m http.server 8000
```

A phishing email impersonating the organization's IT Support team instructed the victim to download the PowerShell diagnostic tool.

---

**Figure 4.** Simulated phishing email used to deliver the payload.

<p align="center">
<img src="screenshots/delivery/phishing_email.png" width="900">
</p>

---

The attacker verified successful delivery by monitoring the Python HTTP server logs.

---

**Figure 5.** Python HTTP server log confirming the victim successfully downloaded the payload.

<p align="center">
<img src="screenshots/delivery/http_server.png" width="900">
</p>

---

## Summary

The attacker successfully completed the first three phases of the Cyber Kill Chain:

- **Reconnaissance:** Identified exposed Windows services through network scanning.
- **Weaponization:** Prepared a benign PowerShell diagnostic script to simulate attacker behavior.
- **Delivery:** Delivered the payload using a phishing email and confirmed successful download through HTTP server logs.

The generated telemetry provides realistic artifacts for investigation using Splunk Enterprise, Sysmon, and Windows Firewall logs.
