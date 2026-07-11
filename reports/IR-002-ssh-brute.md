# Incident Report — IR-002

| Field | Details |
|-------|---------|
| **Report ID** | IR-002 |
| **Date** | <!-- YYYY-MM-DD --> |
| **Analyst** | <!-- Your name --> |
| **Severity** | Medium |
| **Status** | Closed |
| **Attack Type** | Network Port Scan |
| **Detection Source** | Suricata IDS / Zeek NSM |

---

## Executive Summary

<!--
2-3 sentences summarising what happened.
Example: "A systematic port scan was detected originating from [IP] targeting [IP]. Suricata identified the scan pattern and fired an ET SCAN rule. Investigation using Zeek connection logs confirmed the scan covered X ports over Y seconds."
-->

---

## Timeline of Events

| Time | Event |
|------|-------|
| <!-- HH:MM --> | <!-- What happened --> |
| | |
| | |
| | |

---

## Technical Analysis

<!--
- What type of scan was it? (SYN scan, full connect, UDP, version detection)
- How did you identify it as a scan vs normal traffic?
- What did the Suricata alert show?
- What did the Zeek conn.log show? (number of connections, ports targeted, timing)
- What tool was likely used? How do you know?
- What was the attacker trying to discover?
-->

---

## Indicators of Compromise (IOCs)

| Type | Value | Notes |
|------|-------|-------|
| Source IP | | |
| Target IP | | |
| Ports scanned | | |
| Scan tool identified | | |
| Scan duration | | |

---

## MITRE ATT&CK Mapping

| Technique ID | Technique Name | Notes |
|-------------|----------------|-------|
| T1595 | Active Scanning | |
| T1046 | Network Service Discovery | |

---

## Detection Details

**Alert fired by:** Suricata  
**Rule set:** Emerging Threats  
**Rule triggered:** <!-- exact rule name -->  
**Zeek log:** conn.log  

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
List screenshots and logs used.
-->

---

*Report written by: <!-- Your name -->*  
*Review date: <!-- Date -->*
