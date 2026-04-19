# Incident Report — IR-003

| Field | Details |
|-------|---------|
| **Report ID** | IR-003 |
| **Date** | <!-- YYYY-MM-DD --> |
| **Analyst** | <!-- Your name --> |
| **Severity** | High |
| **Status** | Closed |
| **Attack Type** | Honeypot Compromise & Post-Exploitation |
| **Detection Source** | Cowrie Honeypot / Wazuh |

---

## Executive Summary

<!--
2-3 sentences. This one is the most interesting report — an attacker actually "got in" (to the honeypot) and you got to watch everything they did.
-->

---

## Timeline of Events

| Time | Event |
|------|-------|
| | Attacker begins scanning |
| | First login attempt |
| | Successful honeypot login |
| | First command executed |
| | <!-- what else happened --> |
| | Session ended |

---

## Technical Analysis

<!--
This is the most detailed report. Cover:
- How did the attacker find the honeypot?
- What credentials did they use to log in?
- What commands did they run after logging in? (list them)
- What were they trying to do? (recon? install malware? establish persistence?)
- Did they try to download anything?
- How long was the session?
- Use bin/playlog to replay the session and describe what you saw
-->

---

## Attacker Commands Observed

<!--
List the actual commands the attacker ran inside the honeypot.
This is captured by Cowrie in the session logs.
-->

```
<!-- paste commands here -->
```

---

## Indicators of Compromise (IOCs)

| Type | Value | Notes |
|------|-------|-------|
| Source IP | | AbuseIPDB score: |
| Country | | |
| Username used | | |
| Password used | | |
| Files downloaded | | |
| URLs contacted | | |

---

## MITRE ATT&CK Mapping

| Technique ID | Technique Name | Notes |
|-------------|----------------|-------|
| T1110 | Brute Force | Initial access |
| T1059 | Command and Scripting Interpreter | Post-exploitation |
| <!-- ID --> | <!-- Based on what commands they ran --> | |

---

## Response Actions Taken

<!--
1.
2.
3.
-->

---

## Recommendations

<!--
1.
2.
3.
-->

---

## Evidence

<!--
- Cowrie session replay: bin/playlog var/lib/cowrie/tty/<session_id>
- Screenshots
- Log excerpts
-->

---

*Report written by: <!-- Your name -->*  
*Review date: <!-- Date -->*
