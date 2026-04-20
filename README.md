# Home SOC Lab

> A fully functional Security Operations Centre built on a main PC and Raspberry Pi using 100% free and open source tools.

---

## Overview

<!-- 
Write 3-4 sentences in your own words:
- What is this project?
- Why did you build it?
- What does it demonstrate?
Example: "This project is a home SOC lab I built to gain hands-on experience with..."
-->

---

## Architecture

<!-- Add your network diagram image here once you create it in draw.io -->
<!-- ![Network Diagram](architecture/network-diagram.png) -->

| Device | Role | Real Network | Lab Network |
|--------|------|--------------|-------------|
| Main PC (Ubuntu VM) | SIEM, IR Platform, SOAR, Threat Intel | 192.168.1.100 | 192.168.100.10 |
| Raspberry Pi 4 | Network Sensor, IDS, Honeypot | 192.168.1.10 | NA |
| Attack VM | Simulated target (Metasploitable) | NA | 192.168.100.20 |

---

## Tools & Their Purpose

| Tool | Category | Purpose |
|------|----------|---------|
| Wazuh | SIEM | <!-- Explain in your own words what Wazuh does in your setup --> |
| ELK Stack | Visualization | <!-- Your explanation --> |
| Suricata | IDS | <!-- Your explanation --> |
| Zeek | NSM | <!-- Your explanation --> |
| Cowrie | Honeypot | <!-- Your explanation --> |
| TheHive | Incident Response | <!-- Your explanation --> |
| Shuffle SOAR | Automation | <!-- Your explanation --> |
| MISP | Threat Intelligence | <!-- Your explanation --> |
| Ollama + Llama 3 | AI/ML | <!-- Your explanation --> |
| scikit-learn | ML | <!-- Your explanation --> |

---

## What I Built

<!--
Write this in your own words. Not a list of tools — a description of what the system actually does end to end.
Example: "Traffic on my home network is monitored in real time by Suricata running on the Pi..."
-->

---

## Key Features

<!--
List 4-6 things you're most proud of. Write them yourself.
Examples:
- Automated alert triage pipeline that enriches every alert with threat intel and AI analysis
- Local LLM running on-premises for privacy-preserving AI with zero cost
-->

---

## Challenges & How I Solved Them

<!--
Pick 2-3 of the most interesting problems you solved during this project.
Write them as short stories — what happened, how you diagnosed it, how you fixed it.
The full list is in troubleshooting.md — pick the best ones for here.
-->

---

## Skills Demonstrated

<!--
List the SOC competencies this project covers.
Be specific — not just "networking" but "network traffic analysis using Zeek and Suricata"
-->

- 
- 
- 
- 
- 

---

## Incident Reports

| Report | Attack Type | Severity | Date |
|--------|------------|----------|------|
| [IR-001](reports/IR-001-ssh-bruteforce.md) | SSH Brute Force | High | |
| [IR-002](reports/IR-002-port-scan.md) | Port Scan | Medium | |
| [IR-003](reports/IR-003-honeypot-compromise.md) | Honeypot Compromise | High | |

---

## Screenshots

<!--
Add screenshots here once you have them.
Suggested screenshots:
- Kibana/Wazuh dashboard showing alerts
- Suricata alert in ELK
- TheHive case with AI analysis
- Shuffle SOAR playbook canvas
- Cowrie honeypot session capture
-->

---

## Lessons Learned

<!-- Link to your full lessons learned document -->
See [lessons-learned.md](lessons-learned.md)

---

## Author

**Your Name**  
[LinkedIn](#) · [GitHub](#)
