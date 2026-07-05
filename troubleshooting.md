# Troubleshooting Log

A record of every significant issue encountered during this project, how it was diagnosed, and what fixed it.

---

## Issue 01 — Router DHCP Reservation Not Showing Up

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** 4-4-26

**Symptom:**  
Added a DHCP reservation in the NetLink router's "Edit Reserved IP Address" page but the entry didn't appear in the list after saving.

**Investigation:**  
After the reservation failed to appear, I ran `ifconfig` on the Raspberry Pi to inspect all active network interfaces. This revealed that the Pi had two network interfaces — `eth0` (ethernet) and `wlan0` (WiFi). The `eth0` interface had no IP address assigned, while `wlan0` had an active IP address of `192.168.1.46`. This meant the Pi was connected to the network via WiFi, not ethernet.

The MAC address I had entered into the router's reservation page was the `eth0` MAC address, but since the Pi was connecting through `wlan0`, the router was seeing a completely different MAC address.

**Root Cause:**  
The MAC address entered belonged to `eth0` (the ethernet interface) but the Raspberry Pi was actually connected to the network via WiFi (`wlan0`). Each network interface has its own unique MAC address — the router only sees the interface that is actively connected, which was `wlan0`, not `eth0`.

**Fix:**  
Identified the correct MAC address for `wlan0` from the ifconfig output and used that in the router's reservation page instead. Since we later switched to ethernet, we ultimately used `nmcli` to set a static IP directly on the Pi instead, which bypasses the router reservation entirely.

**Lesson Learned:**  
Always verify which network interface is actively connected before configuring MAC-based settings. Use `ifconfig` or `ip a` to check which interface has an assigned IP address — that is the active one.

---

## Issue 02 — Static IP Not Applying After Editing dhcpcd.conf

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** 4-4-26

**Symptom:**  
Edited `/etc/dhcpcd.conf` with static IP settings but the Pi's IP address did not change after reboot.

**Investigation:**  
Attempted to restart the dhcpcd service to force the configuration to apply:

`sudo systemctl restart dhcpcd`

This returned the error:

`Failed to restart dhcpcd.service: Unit dhcpcd.service not found`.

This error confirmed that dhcpcd was not installed or running on the system at all, which explained why editing `dhcpcd.conf` had no effect — the file was simply being ignored.

Ran `nmcli -v` which returned a version number, confirming that NetworkManager was the active network manager on this system.

**Root Cause:**  
Raspberry Pi OS Bookworm (released 2023) replaced `dhcpcd` with `NetworkManager` as the default network manager. The `dhcpcd.conf` file is completely ignored on this OS version because the service doesn't exist.

**Fix:**  
Used `nmcli` (NetworkManager CLI) to configure a static IP address for the Ethernet connection profile. Verified the configuration using `ip a`, confirming that the static IP `192.168.1.10` was successfully assigned to the network interface.

**Lesson Learned:**  
Always check the OS version before following network configuration guides. Many online guides still reference `dhcpcd` which is outdated for Raspberry Pi OS Bookworm. Verify the active network manager with `nmcli -v` before making changes.

---

## Issue 03 — VNC Not Connecting After Switching to Ethernet

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** 4-4-26

**Symptom:**  
After plugging an ethernet cable into the Raspberry Pi, RealVNC Viewer on the main PC could no longer connect to the Pi. The connection attempt timed out with no response.

**Investigation:**  The Pi had previously been connected via WiFi with an IP address of `192.168.1.46`. RealVNC Viewer was configured to connect to this address. When the ethernet cable was plugged in, the Pi's active interface changed from `wlan0` to `eth0`, which received a new DHCP-assigned IP address of `192.168.1.39`.

Since RealVNC was still pointing at the old WiFi IP `192.168.1.46`, which was no longer the active address, the connection attempt had nowhere to go.

**Root Cause:**  
Switching from WiFi (`wlan0`) to ethernet (`eth0`) caused the Pi to obtain a new IP address from the router's DHCP pool. The old WiFi IP was released and the ethernet interface received a different address. Any remote connection tool (VNC, SSH) configured with the old IP will fail after such a change.

**Fix:**  
Updated the RealVNC Viewer connection address from the old WiFi IP (`192.168.1.46`) to the new ethernet IP (`192.168.1.39`). Verified the identity signature in the duplicate server warning matched the known Pi before clicking Continue.

Subsequently set a permanent static IP (`192.168.1.10`) on the ethernet interface using `nmcli` so that the IP never changes on reboot, preventing this issue from occurring again.

**Lesson Learned:**  
Whenever a device switches network interfaces or reconnects to the network, its IP address may change. Always run `ip a` after any network change to verify the current IP before attempting a remote connection. Setting a static IP permanently resolves this — once set, the Pi always uses `192.168.1.10` regardless of how many times it reboots or reconnects.

---

## Issue 04 — Not getting internet connectivity on Ubuntu VM after setting static IP 

**Phase:** Phase 1 — Prerequisites & Planning  
**Date:** 21-4-26

**Symptom:**  
Internet connectivity on ubuntu VM got disabled after changing to the static IP `192.168.1.100`.

**Investigation:**  After running ping `8.8.8.8`, it returned 'destination host unreachable', confirming that it was a routing issue rather than a DNS issue. Running `ipconfig` on the Windows host revealed that the VM was on NAT mode which uses VMware's internal virtual network.

**Root Cause:**  
The VM was configured in NAT networking mode. NAT creates an internal virtual network between the VM and the host — the VM's traffic goes through VMware's virtual NAT device, not directly through the real router.

**Fix:**  
Updated the network connection to Bridged under 'virtual network editor' in VMware settings and chose the host machine's Wi-Fi adapter. Under the VM settings changed the network adapter from NAT to Bridged.

**Lesson Learned:**  
When setting a static IP on a VM, always first confirm which networking mode the VM is using before configuring the gateway. NAT and Bridged modes have fundamentally different network architectures — NAT uses VMware's internal virtual gateway while Bridged connects directly to the real network and uses the physical router as the gateway. Setting a gateway that doesn't match the networking mode will break internet connectivity.

---

## Issue 05 — UFW lockout 

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** 02-05-26

**Symptom:**  
Could not SSH or VNC into Pi after enabling UFW.

**Root Cause:**  
The firewall was configured with a highly restrictive rule set that only recognized and permitted incoming traffic from the Ubuntu virtual machine's IP address. Because the primary Windows workstation used a separate host IP on the subnet, its direct connection attempts were dropped by default. Additionally, the explicit network port required for VNC traffic 5900 was completely omitted from the rule.

**Fix:**  
Because all network-based administration channels were locked down, recovery required local out-of-band management. A physical monitor and keyboard were plugged directly into the Raspberry Pi hardware to access the local command line interface without using the network card. From this local shell, targeted rules were executed to allow traffic from the Windows host IP address and to open port 5900 for VNC access, restoring full remote functionality.

**Lesson Learned:**  
Always verify you can still connect remotely before enabling UFW, and add all required ports before enabling the firewall.

---

## Issue 06 — Pending kernel upgrade dialog persisted after multiple reboots 

**Phase:** Phase 4: IDS — Suricata & Zeek  
**Date:** 02-05-26

**Symptom:**  
The system repeatedly displayed a notification warning that a kernel upgrade was pending, even after the machine was successfully restarted multiple times.

**Root Cause:**  
Standard update routines (like `apt upgrade`) are designed to safely update existing packages, but they will not install new packages or remove old ones if dependencies change.

**Fix:**  
Executing `sudo apt full-upgrade` allowed the package manager to intelligently handle the system dependencies, installing the necessary new kernel components and removing obsolete files. This cleared the package log jam, fully initialized the new kernel upon the next reboot, and successfully resolved the persistent dialog warning.

**Lesson Learned:**  
When dealing with core Linux components like the kernel, standard upgrade commands are often insufficient to clear deep dependency shifts. We should utilize `full-upgrade` or `dist-upgrade` when critical packages become held back, ensuring the OS can safely restructure its components without leaving notifications broken.

---

## Issue 07 — Suricata not generating alerts despite rules being loaded 

**Phase:** Phase 4: IDS — Suricata & Zeek  
**Date:** 02-05-26

**Symptom:**  
The Suricata intrusion detection engine failed to trigger any security alerts or log events, even though the custom signature files had been created on the system. The service appeared to be running cleanly in the background, but it completely ignored simulated attack traffic designed to trip the sensor.

**Root Cause:**  
While the custom signature file (`local.rules`) was successfully written to the directory, it was completely ignored by the engine because it was never declared in the primary configuration file. Suricata relies strictly on the `rule-files` array inside `suricata.yaml` to build its active detection matrix, meaning any unlisted rules file is simply skipped during initialization.

**Fix:**  
The configuration file (`/etc/suricata/suricata.yaml`) was opened, and a new line entry pointing to - local.rules was explicitly added under the existing rule-files block. After saving the changes, restarting the Suricata service forced the engine to parse the freshly linked file, instantly enabling alert generation for the custom rules.

**Lesson Learned:**  
Creating a rules file alone is not enough — Suricata only loads rules files explicitly listed in `suricata.yaml`.

---

## Issue 08 — PATH changes not working with sudo commands

**Phase:** Phase 4: IDS — Suricata & Zeek  
**Date:** 05-05-26

**Symptom:**  
Custom environment variable configurations and terminal PATH extensions completely failed to register whenever commands were run with administrative privileges.

**Root Cause:**  
By default, Linux security configurations utilize a policy called secure_path inside the /etc/sudoers file, which completely wipes out the user's custom environment variables during privilege escalation.

**Fix:**  
To bypass the restricted environment, commands were executed by explicitly calling their absolute filesystem locations (such as sudo /usr/local/bin/my-tool) rather than relying on short names. Alternatively, a permanent bash alias can be mapped to the absolute path within the user profile shell configuration to preserve streamlined terminal workflow.

**Lesson Learned:**  
Always use full paths in scripts and aliases when dealing with non-standard installation locations.

---

## Issue 09 — Filebeat service on Raspberry Pi crashes repeatedly with "Start request repeated too quickly" error

**Phase:** Phase 4: IDS — Suricata & Zeek  
**Date:** 15-05-26

**Symptom:**  
The Filebeat daemon on the Raspberry Pi network sensor entered an immediate crash loop, completely failing to sustain a running state (`status=1/FAILURE`). System log utilities showed the service continuously dropping offline upon initialization, triggering systemd's built-in protective throttling due to the rapid, infinite cycle of restart failures.

**Root Cause:**  
The failure was driven by a combination of configuration and protocol errors. First, an incorrect indentation layout under the `paths` key on line 19 of `filebeat.yml` broke the strict YAML configuration parser. Second, the standard Elastic-licensed distribution of Filebeat aggressively queried a proprietary commercial license index endpoint; because the backend target was a open-source Wazuh Indexer (OpenSearch), it rejected the request with an `invalid_index_name_exception` 400 error, causing the agent to panic and crash.

**Fix:**  
The proprietary Elastic Filebeat package was completely purged from the system to eliminate the incompatible licensing codebase. It was replaced by manually installing the official, open-source distribution (`filebeat_7.10.2_arm64.deb`) provided by Wazuh, which lacks the commercial tracking loops. Finally, the configuration file was rewritten using perfect space-based alignment for the `type, enabled, paths`, and `tags` keys while pointing cleanly to the indexer port using admin credentials.

**Lesson Learned:**  
Proprietary security monitoring agents and open-source backend forks (like OpenSearch and Wazuh Indexer) cannot perform standard telemetry handshakes due to hardcoded software checkpoints. When architecting a log shipping pipeline from a remote network sensor to an open-source SIEM cluster, you must always deploy explicit open-source (OSS) agent binaries to guarantee protocol alignment.

---

## Issue 10 — PATH changes not working when executing Zeek commands with sudo

**Phase:** Phase 6: Incident Response Setup  
**Date:** 16-05-26

**Symptom:**  
Administrative commands such as sudo zeekctl status failed completely, returning command not found errors in the terminal interface. While the network sensor utilities executed perfectly under a standard user account, privilege escalation broke the execution pipeline.

**Root Cause:**  
The security architecture of `sudo` actively sanitizes and resets the PATH environment variable to protect the root environment from local user-level path manipulation. Because Zeek was deployed to a non-standard location (`/opt/zeek/bin`), the hardened administrative sub-shell could not automatically resolve the location of the `zeekctl` binary.

**Fix:**  
The restriction was bypassed by explicitly prefixing the command with its absolute, fully qualified directory location: `sudo /opt/zeek/bin/zeekctl status`. By pointing directly to the exact home folder of the target execution file, the system no longer needed to look up the binary within the stripped down environment variables. For regular operational convenience, long paths can also be permanently mapped as administrative system aliases within the active profile shell.

**Lesson Learned:**  
Sudo environment isolation ignores custom user-level PATH modifications for security validation.

---

## Issue 11 — Wazuh log collection failed with "No such file or directory" error while zeekctl falsely reported a service crash

**Phase:** Phase 6: Incident Response Setup  
**Date:** 17-05-26

**Symptom:**  
Wazuh failed to collect data because the http.log file was missing from its normal path, while zeekctl wrongly reported that the service had crashed.

**Root Cause:**  
Zeek was manually started multiple times directly on the network card while the automated manager tool was also running. This process clash blinded the service status tracker and forced Zeek to save its active logs into the user's current working directory instead of the correct system folder.

**Fix:**  
Ran sudo `pkill -f /opt/zeek/bin/zeek` to force-stop all conflicting background processes. Cleaned out the broken temporary tracking files, restarted the service cleanly using the proper `zeekctl` tool, and restarted `wazuh-agent` to read the fixed log path.

**Lesson Learned:**  
Running network sniffers manually bypasses system configurations and breaks automated management tools. Always use standard orchestration tools (`zeekctl`) to deploy services cleanly, avoid race conditions, and keep SIEM data flowing properly.

---

## Issue 12 — Wazuh dashboard showing AxiosError and "No API available

**Phase:** Phase 6: Incident Response Setup  
**Date:** 17-05-26

**Symptom:**  
The Wazuh dashboard suddenly stopped working, displaying an `AxiosError` and reporting a "No API available" status message. The entire user interface became inaccessible.

**Root Cause:**  
Custom detection rules added to `local_rules.xml` included a dependency reference to a `cowrie` log decoder that did not actually exist in the system. Because the rules engine could not resolve this missing link, the core analysis daemon (`wazuh-analysisd`) crashed immediately upon initialization, which subsequently knocked out the internal API management service.

**Fix:**  
The broken rules file and the incomplete decoder configuration were deleted from the system manager path. This allowed the Wazuh engine to automatically recreate the files with clean, factory-default rulesets, allowing the core analysis daemon and API stack to boot back up successfully.

**Lesson Learned:**  
Never save new custom detection rules or decoders to production without validating them beforehand using the `wazuh-logtest` utility. A single syntax error, structural typo, or invalid decoder reference will cause the entire Wazuh analysis cluster to crash on startup.

---

## Issue 13 — Custom Wazuh cowrie rules not matching — generic rule 1002 firing instead

**Phase:** Phase 6: Incident Response Setup  
**Date:** 17-05-26

**Symptom:**  
Custom rules designed specifically for Cowrie honeypot traffic failed to match incoming events, causing the SIEM to fire generic rule 1002 alerts instead.

**Root Cause:**  
A custom Cowrie decoder was created to parse the traffic, but Wazuh's native, built-in JSON decoder matched the incoming log streams first and took priority. Because the system's internal evaluation logic stopped at the first successful match, the custom decoder was completely bypassed, leaving the custom rules without the correct decoder link needed to fire.

**Fix:**  
The redundant custom decoder was deleted entirely from the system layout, and the detection strategy was shifted to utilize native components. The custom rules were rewritten to explicitly declare `<decoded_as>json</decoded_as>` while using field filters like <`field name="eventid">cowrie\.</field>` to cleanly isolate Cowrie events out of the standard JSON telemetry stream.

**Lesson Learned:**  
Wazuh's built-in JSON decoder automatically takes processing priority over custom configurations when parsing structured data strings. When dealing with logs that are already formatted as valid JSON, you should filter by specific internal field values rather than trying to build a new decoder from scratch.

---

## Issue 14 — Wazuh Manager File System Permission Denied

**Phase:** Phase 6: Incident Response Setup  
**Date:** 19-05-26

**Symptom:**  
The Wazuh integration daemon completely failed to trigger the triage script, throwing a Permission denied (os.error 13) inside the manager's master integration logs.

**Root Cause:**  
The script file (`alert_triage.py`) was successfully copied from the Ubuntu host VM into the running Docker manager container, but it retained its original restricted user permissions. Because the containerized integration daemon runs under a restricted system user account (`ossec`), it lacked the necessary read and execute rights to launch the file.

**Fix:**  
`docker exec -u root single-node-wazuh.manager-1 chmod 755 /var/ossec/integrations/custom-l1-triage` to explicitly elevate the file's permissions, granting the manager's internal daemon global execution access.

**Lesson Learned:**  
Moving files into isolated container environments resets or breaks access inheritances. Always explicitly apply broad execution permissions (`chmod +x`) inside the container destination using root elevation to ensure background services can interact with custom code.

---

## Issue 15 — Zeek Log File Rotation Breaking Wazuh Symlink Tracking

**Phase:** Phase 7: Attack Simulation & Evidence  
**Date:** 21-05-26

**Symptom:**  
Wazuh successfully ingested Zeek data for the first hour of operation, but suddenly stopped updating alerts completely at the turn of the next hour.

**Root Cause:**  
Zeek uses a default scheduling task that automatically archives active log data every 60 minutes, moving the files out of `/opt/zeek/logs/current/` and replacing them with fresh, blank files. The `wazuh-agent` configuration was pointing directly to the hard file path instead of tracking the active folder pointer, causing it to read from an abandoned, dead file descriptor.

**Fix:**  
Updated the `ossec.conf` file on the network sensor to use wildcard directory monitoring configurations (`/opt/zeek/logs/current/*.log`) and configured the reader to aggressively check for file modifications.

**Lesson Learned:**  
Network analysis tools that rotate data streams dynamically break static file readers. Log collection agents must be explicitly configured to monitor active directories using wildcards or symlink trackers rather than watching rigid, fixed filenames.

---

## Issue 16 — Localhost Port Conflict on Host VM Binding Ollama

**Phase:** Phase 08: AI Integration 
**Date:** 28-05-26

**Symptom:**  
Starting the local AI model returned an `Address already in use` bind error on port `11434`, completely preventing Llama 3 from booting up.

**Root Cause:**  
Multiple terminal tabs or automated systemd background services had initialized the Ollama server framework concurrently on the Ubuntu VM. Because a previous test instance was still running silently in the background, it maintained an exclusive lock on the required network communication port.

**Fix:**  
Identified the hidden background process ID using `sudo ss -tulpn | grep 11434` and killed the active process block using `sudo kill -9 <PID>` to clear the port line for a clean deployment.

**Lesson Learned:**  
Background daemons running in modern virtualization sandboxes frequently leave orphan processes behind when terminal tabs are closed improperly. Always audit active port usage profiles before attempting to relaunch high-memory intelligence architectures.

---

## Issue 17 — Zeek Interface Promiscuous Mode Dropping Telemetry

**Phase:** Phase 06: Incident Response Setup 
**Date:** 28-05-26

**Symptom:**  
Zeek stayed completely active and stable, but its connection logs (`conn.log`) remained empty or only recorded the Pi's own native loopback traffic.

**Root Cause:**  
The Raspberry Pi's physical network interface card (`eth0`) was not configured to run in promiscuous mode. Without this setting active at the kernel level, the card automatically discarded all surrounding network packets on the switch that were not explicitly addressed to the Pi's own MAC address.

**Fix:**  
Executed `sudo ip link set eth0 promisc on` in the system shell to force the hardware adapter to capture all raw traffic passing through the mirrored network switch port.

**Lesson Learned:**  
Network sniffing deployments will remain functionally blind if the underlying hardware interface is left in standard routing mode. Promiscuous mode configuration must be explicitly enabled at the operating system level to ensure raw packet access.

---