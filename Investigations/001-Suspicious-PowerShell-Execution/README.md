# SOC Case 001 – Suspicious PowerShell Execution

## Scenario

A PowerShell execution alert was generated on the endpoint **SOC-WIN10**. The objective was to determine whether the execution was malicious or legitimate.

---

# Environment

| Component | Value |
|-----------|-------|
| SIEM | Splunk Enterprise 10.4.2 |
| Endpoint | Windows 10 |
| Logging | Sysmon |
| Data Source | Sysmon Event ID 1 |

---

# Investigation

## Step 1 – Identify PowerShell Execution

### SPL Query

```spl
index=endpoint
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=1
"C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe"
| table _time Image ParentImage CommandLine User ProcessGuid
```

### Findings

- PowerShell executed successfully.
- Parent process was **explorer.exe**.
- User was **SOC-WIN10\SOC**.

![PowerShell Process](screenshots/02-powershell-process.png)

---

## Step 2 – Investigate Child Processes

### SPL Query

```spl
index=endpoint
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventCode=1
ParentImage="*powershell.exe"
| table _time Image ParentImage CommandLine User
```

### Findings

The following child processes were observed:

| Time | Process | Command |
|------|---------|---------|
| 05:02:32 | whoami.exe | whoami |
| 05:02:36 | hostname.exe | hostname |
| 05:02:40 | ipconfig.exe | ipconfig /all |

![Child Processes](screenshots/03-child-processes.png)

---

# MITRE ATT&CK

| Technique | Description |
|-----------|-------------|
| T1059.001 | PowerShell |
| T1033 | System Owner/User Discovery |
| T1082 | System Information Discovery |
| T1016 | System Network Configuration Discovery |

---

# Analyst Assessment

The investigation determined that PowerShell was launched interactively by the logged-in user (`SOC-WIN10\SOC`) through `explorer.exe`. During the session, several discovery commands (`whoami`, `hostname`, and `ipconfig /all`) were executed.

These commands are commonly observed during the discovery phase of attacker activity but, in this case, were intentionally executed as part of a controlled SOC lab exercise.

No evidence of persistence, privilege escalation, lateral movement, or command-and-control activity was identified.

**Verdict:** Benign (Lab Simulation)

---

# Skills Demonstrated

- Splunk SPL
- Sysmon Analysis
- Process Tree Analysis
- Windows Endpoint Investigation
- MITRE ATT&CK Mapping
