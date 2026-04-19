# SOC Runbooks

A runbook is a checklist of steps to follow when a specific alert fires. Unlike an incident report (which tells the story of what happened), a runbook is a quick reference guide for responding consistently every time.

---

## Runbook 001 — SSH Brute Force

**Trigger:** Wazuh rule 5763 fires / Cowrie logs multiple failed login attempts  
**Severity:** High  
**SLA:** Investigate within 15 minutes

### Detection Checklist
- [ ] Note source IP and timestamp from alert
- [ ] Check how many attempts were made and over what time period
- [ ] Identify which service was targeted (real SSH port 22 or honeypot port 2222)

### Investigation Steps
- [ ] Look up source IP on AbuseIPDB — note confidence score and country
- [ ] Check Zeek conn.log for other connections from the same source IP
- [ ] Check if the IP appears in any MISP threat intel feeds
- [ ] Determine if any login attempts succeeded (check Cowrie session logs)
- [ ] If succeeded — replay the session with `bin/playlog` and document all commands run

### Containment
- [ ] If targeting real systems — block source IP via UFW
- [ ] If targeting honeypot only — continue monitoring, document IOCs in MISP
- [ ] Create TheHive case with all findings

### Documentation
- [ ] Fill in IR template with full timeline and IOCs
- [ ] Add source IP to MISP as an indicator
- [ ] Update this runbook if new patterns were observed

---

## Runbook 002 — Port Scan Detected

**Trigger:** Suricata ET SCAN rules fire  
**Severity:** Medium  
**SLA:** Investigate within 30 minutes

### Detection Checklist
- [ ] Note source IP, target IP, and scan type from Suricata alert
- [ ] Note timestamp and duration

### Investigation Steps
- [ ] Check Zeek conn.log — how many ports were scanned?
- [ ] Identify scan tool if possible (timing patterns, TTL, TCP flags)
- [ ] Check if source IP has previously appeared in other alerts
- [ ] Determine what the attacker was looking for (what ports did they find open?)
- [ ] Check AbuseIPDB for source IP reputation

### Containment
- [ ] If internal source — investigate that device for compromise
- [ ] If external source — note for threat intel, block if persistent
- [ ] Create TheHive case

### Documentation
- [ ] Fill in IR template
- [ ] Note which ports were found open — review if any should be closed

---

## Runbook 003 — Honeypot Login Success

**Trigger:** Cowrie logs a successful login (eventid: cowrie.login.success)  
**Severity:** High  
**SLA:** Investigate within 10 minutes

### Detection Checklist
- [ ] Note source IP, username, password used, timestamp
- [ ] Confirm it was the honeypot (port 2222) not real SSH (port 22)

### Investigation Steps
- [ ] Replay the attacker session: `bin/playlog var/lib/cowrie/tty/<session_id>`
- [ ] Document every command the attacker ran
- [ ] Note any files they tried to download or URLs they contacted
- [ ] Check if any malware was uploaded — check Cowrie's `dl/` folder
- [ ] Look up source IP on AbuseIPDB and VirusTotal
- [ ] Check Zeek for any other activity from same IP

### Containment
- [ ] Honeypot is isolated — no containment needed for real systems
- [ ] If attacker downloaded malware — submit hash to VirusTotal
- [ ] Document all IOCs in MISP

### Documentation
- [ ] Fill in IR-003 template with full command list
- [ ] Export MISP event with all IOCs and ATT&CK tags
- [ ] This is your best portfolio piece — document thoroughly

---

## Runbook 004 — Anomaly Detected in Zeek Logs (ML)

**Trigger:** Isolation Forest anomaly detection script flags unusual connection  
**Severity:** Medium (investigate to determine actual severity)  
**SLA:** Investigate within 1 hour

### Detection Checklist
- [ ] Note flagged source IP, destination IP, destination port
- [ ] Note what made it anomalous (unusual bytes, duration, timing)

### Investigation Steps
- [ ] Check if this IP appears in other logs around the same time
- [ ] Look at Suricata alerts for the same timeframe — did a signature also fire?
- [ ] Check AbuseIPDB and MISP for source IP
- [ ] Determine if anomaly is a true positive or false positive
- [ ] If true positive — escalate to full incident investigation

### False Positive Assessment
- [ ] Is this a known scheduled task or backup job?
- [ ] Is this a new device on the network?
- [ ] Is this expected software update traffic?

### Documentation
- [ ] Note outcome (true positive / false positive) in log
- [ ] If false positive — tune contamination parameter in anomaly script
- [ ] If true positive — open TheHive case and follow relevant runbook

---

<!--
ADD NEW RUNBOOKS AS YOU ENCOUNTER NEW ATTACK TYPES.
-->
