# Setup Notes

Personal notes written during the build process. Not a tutorial — a record of decisions made, configurations chosen, and things learned along the way.

---

## Phase 1 — Prerequisites & Planning

**Date:**  21-4-26

### Main Machine — HP Laptop
- **CPU:** AMD Ryzen 5 5500U (6 cores, 12 threads)
- **RAM:** 16 GB
- **Storage:** ~140 GB available
- **OS:** Windows 11 (host)

### Virtual Machines (running on laptop)
All SOC platform tools run inside VMs on the laptop:
- **Ubuntu 22.04 VM** — SIEM, incident response, SOAR, 
  threat intelligence, AI integration
  - Allocated: 8 GB RAM, 3 cores, 85 GB storage
  - Network: Bridged (192.168.1.100) + Host-Only (192.168.100.10)
- **Metasploitable 2 VM** — attack simulation target
  - Allocated: 512 MB RAM, 1 core
  - Network: Host-Only only (192.168.100.20) — isolated from 
    home network for safety

### Network Sensor — Raspberry Pi 5
- **RAM:** 8 GB
- **Storage:** 234 GB available
- **OS:** Raspberry Pi OS Bookworm (64-bit)
- **Connection:** Ethernet
- **Static IP:** 192.168.1.10
- **Role:** Runs Suricata IDS, Zeek NSM, Cowrie honeypot, 
  and Wazuh agent — all network-facing tools run here

### Why This Split?
The Pi handles all network-facing and sensor work because it sits permanently on the network as a dedicated low-power device. The laptop hosts the analysis and response platform since those tools need more RAM and processing power. Metasploitable is kept on an isolated Host-Only network to prevent its intentional vulnerabilities from exposing the home network to risk.

### Docker installation
Wazuh and ELK will run inside Docker, which is an isolated container where we can run applications with all its dependencies, avoiding conflicts with other software on the system. Docker Compose uses a single YAML file containing all the settings required to easily start and stop multiple services together with one command. The `-d` flag runs containers in the background so the terminal remains usable. By adding myself to the docker group, I don't need to run `sudo` each time I run a docker command. Verified the installation worked by running `docker run hello-world` without `sudo`.
<!--
Write in your own words:
- What hardware you're using and why
- What your network looks like (IPs assigned, why you chose them)
- Any decisions you made about the setup before starting
-->

---

## Phase 2 — Raspberry Pi Setup

**Date:**  

**OS Version:** Raspberry Pi OS Bookworm (64-bit)  
**Pi Model:** <!-- Pi 3B+ / Pi 4B / other -->  
**Connection:** Ethernet (eth0)  
**Static IP:** 192.168.1.10  

<!--
Write in your own words:
- How you flashed the OS
- How you configured the static IP and what issues you ran into
- What hardening steps you took and why each one matters
- What Filebeat is and why it's installed here
-->

---

## Phase 3 — Wazuh + ELK Stack

**Date:**  

<!--
Write in your own words:
- What Wazuh does and why you chose it
- What the ELK stack components each do
- How Docker Compose works and why you used it
- What the agent on the Pi does and how it communicates with the manager
- What dashboards you set up and what they show
-->

---

## Phase 4 — Suricata & Zeek

**Date:**  

<!--
Write in your own words:
- The difference between Suricata and Zeek — they do different things
- What "rule-based detection" means (Suricata) vs "behavioural logging" (Zeek)
- What the Emerging Threats ruleset is
- What Filebeat is doing here — forwarding these logs to Elasticsearch
- What eve.json is and why it matters
-->

---

## Phase 5 — Cowrie Honeypot

**Date:**  

<!--
Write in your own words:
- What a honeypot is and why it's valuable
- Why Cowrie runs as an unprivileged user (security isolation)
- What port 2222 is for and why real SSH stays on port 22
- What the fake filesystem does and why it matters
- What kind of data Cowrie captures
-->

---

## Phase 6 — Incident Response (TheHive)

**Date:**  

<!--
Write in your own words:
- What TheHive does and how it fits into a SOC workflow
- What a "case" is in TheHive
- What your Python triage script does step by step
- What AbuseIPDB is and how you use it
-->

---

## Phase 7 — Attack Simulation

**Date:**  

<!--
Write in your own words:
- What Metasploitable is and why it's safe to attack
- Each attack you ran and what you were trying to achieve
- What tool you used for each attack
- General observations about how quickly alerts fired
-->

---

## Phase 8 — Shuffle SOAR

**Date:**  

<!--
Write in your own words:
- What SOAR means and why it matters in a real SOC
- The difference between your manual Python script and Shuffle
- How the Wazuh webhook integration works
- Walk through your playbook logic step by step
-->

---

## Phase 9 — MISP

**Date:**  

<!--
Write in your own words:
- What threat intelligence is and why SOCs use it
- What an IOC (Indicator of Compromise) is
- What feeds you enabled and what each one provides
- How the Wazuh integration works — what happens when a match is found
- How you documented a honeypot session as a MISP event
-->

---

## Phase 10 — AI Integration

**Date:**  

**LLM:** Llama 3 8B via Ollama  
**ML:** Isolation Forest (scikit-learn)  

<!--
Write in your own words:
- What Ollama is and how it works
- Why running a local LLM is better than a cloud API for this use case
- What each of the four AI features does
- What Isolation Forest is — how does it detect anomalies?
- How the AI alert triage integrates with your existing pipeline
- Quality of the AI output — was it useful? Any limitations?
-->

---
