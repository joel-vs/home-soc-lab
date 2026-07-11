# Incident Report — IR-001

| Field | Details |
|-------|---------|
| **Report ID** | IR-001 |
| **Date** | <!-- YYYY-MM-DD --> |
| **Analyst** | <!-- Your name --> |
| **Severity** | High |
| **Status** | Closed |
| **Attack Type** | SSH Brute Force |
| **Detection Source** | Wazuh / Cowrie Honeypot |

---

## Executive Summary

<!--
Write 2-3 sentences summarising what happened for a non-technical reader.
Example: "On [date], a brute force attack was detected against the SSH honeypot running on the Raspberry Pi sensor node. The attacker attempted [X] login combinations over [Y] minutes using automated tooling. The attack was detected in real time by the Cowrie honeypot and correlated by Wazuh, with no real systems compromised."
-->

---

## Timeline of Events

| Time | Event |
|------|-------|
| <!-- HH:MM --> | <!-- What happened --> |
| | |
| | |
| | |
| | |

---

## Technical Analysis

<!--
Write a detailed explanation of what happened:
- What tool/technique did the attacker use?
- How did you identify it was a brute force attack?
- What did the Cowrie logs show? (commands attempted, credentials tried)
- What did the Wazuh alert look like?
- What did the Suricata/Zeek logs show?
-->

---

## Indicators of Compromise (IOCs)

| Type | Value | Notes |
|------|-------|-------|
| Source IP | <!-- x.x.x.x --> | <!-- AbuseIPDB score, country --> |
| Usernames attempted | <!-- root, admin, ubuntu --> | |
| Passwords attempted | <!-- from wordlist --> | |
| Tool identified | <!-- Hydra, Medusa etc --> | <!-- identified how? --> |

---

## MITRE ATT&CK Mapping

| Technique ID | Technique Name | Notes |
|-------------|----------------|-------|
| T1110 | Brute Force | <!-- Specific sub-technique if applicable --> |
| <!-- ID --> | <!-- Name --> | |

---

## Detection Details

**Alert fired by:** <!-- Wazuh / Cowrie / Suricata -->  
**Rule ID:** <!-- Wazuh rule number -->  
**Rule description:** <!-- exact rule description -->  
**Alert level:** <!-- Wazuh severity level -->  
**Detected at:** <!-- timestamp -->  

---

## Response Actions Taken

<!--
List exactly what you did in response, in order:
1. 
2.
3.
-->

---

## Root Cause

<!--
Why did this happen? What made the system a target?
Example: "The honeypot was intentionally configured to accept SSH connections on port 2222 to attract and log attacker activity."
-->

---

## Recommendations

<!--
If this were a real organisation, what would you recommend?
Write 3-5 specific, actionable recommendations.
1.
2.
3.
-->

---

## Evidence

<!--
List screenshots and log files used in this investigation.
- Screenshot: Wazuh alert dashboard (screenshots/IR-001-wazuh-alert.png)
- Screenshot: Cowrie session replay (screenshots/IR-001-cowrie-session.png)
- Log file: /var/log/cowrie/cowrie.json (excerpt)
-->

---

*Report written by: <!-- Your name -->*  
*Review date: <!-- Date -->*
