# Troubleshooting Log

A record of every significant issue encountered during this project, how it was diagnosed, and what fixed it.

---

## Issue 001 — Router DHCP Reservation Not Showing Up

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** <!-- fill in -->

**Symptom:**  
Added a DHCP reservation in the NetLink router's "Edit Reserved IP Address" page but the entry didn't appear in the list after saving.

**Investigation:**  
<!-- Write in your own words what you tried and what you checked -->

**Root Cause:**  
The MAC address entered belonged to `eth0` (the ethernet interface) but the Raspberry Pi was actually connected to the network via WiFi (`wlan0`). Each network interface has its own unique MAC address — the router only sees the interface that is actively connected, which was `wlan0`, not `eth0`.

**Fix:**  
<!-- Write what you actually did to fix it -->

**Lesson Learned:**  
Always verify which network interface is actively connected before configuring MAC-based settings. Use `ifconfig` or `ip a` to check which interface has an assigned IP address — that is the active one.

---

## Issue 002 — Static IP Not Applying After Editing dhcpcd.conf

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** <!-- fill in -->

**Symptom:**  
Edited `/etc/dhcpcd.conf` with static IP settings but the Pi's IP address did not change after reboot.

**Investigation:**  
<!-- Write what commands you ran to investigate -->

**Root Cause:**  
Raspberry Pi OS Bookworm (released 2023) replaced `dhcpcd` with `NetworkManager` as the default network manager. The `dhcpcd.conf` file is completely ignored on this OS version because the service doesn't exist. Running `systemctl restart dhcpcd` returned "Unit dhcpcd.service not found" which confirmed this.

**Fix:**  
<!-- Write the nmcli command you used and what each part did -->

**Lesson Learned:**  
Always check the OS version before following network configuration guides. Many online guides still reference `dhcpcd` which is outdated for Raspberry Pi OS Bookworm. Verify the active network manager with `nmcli -v` before making changes.

---

## Issue 003 — Wrong Interface in dhcpcd.conf

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** <!-- fill in -->

**Symptom:**  
Configured static IP for `eth0` in `dhcpcd.conf` but Pi was connected via WiFi — IP still didn't change.

**Investigation:**  
<!-- Write what you checked -->

**Root Cause:**  
The config file referenced `eth0` (ethernet) but the Pi was connected via `wlan0` (WiFi). Even if dhcpcd had been the active network manager, the config would have applied to the wrong interface.

**Fix:**  
<!-- Write what you changed -->

**Lesson Learned:**  
`eth0` and `wlan0` are two separate interfaces with separate MAC addresses and separate IP configurations. Always confirm which interface is active with `ip a` before editing any network config.

---

## Issue 004 — VNC Not Connecting After Switching to Ethernet

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** <!-- fill in -->

**Symptom:**  
RealVNC Viewer could not connect to the Raspberry Pi after plugging in an ethernet cable.

**Investigation:**  
<!-- Write what you checked -->

**Root Cause:**  
Switching from WiFi to ethernet gave the Pi a new IP address. RealVNC was still attempting to connect to the old WiFi IP (`192.168.1.46`) which was no longer active.

**Fix:**  
<!-- Write how you found the new IP and reconnected -->

**Lesson Learned:**  
When a device switches network interfaces, its IP address changes. Always verify the current IP with `ip a` after any network change before attempting to reconnect remotely.

---

## Issue 005 — Duplicate VNC Server Identity Warning

**Phase:** Phase 2 — Raspberry Pi Setup  
**Date:** <!-- fill in -->

**Symptom:**  
RealVNC showed a "Duplicate VNC Server identity" warning when connecting to the new ethernet IP.

**Investigation:**  
<!-- Write what the warning said and what you checked -->

**Root Cause:**  
RealVNC had previously connected to this Pi via its WiFi IP. When the same Pi appeared on a new IP (ethernet), VNC flagged it as potentially a different machine impersonating the known one — a legitimate security check.

**Fix:**  
Verified the identity signature and catchphrase matched the known Pi, then clicked Continue. The warning was harmless in this context.

**Lesson Learned:**  
This warning is a useful security feature — it detects when a known device appears on an unexpected IP. In a real network environment this could indicate ARP spoofing or a rogue device. Always verify the signature before continuing.

---

## Issue 006 — Ubuntu VM Not Getting Internet on Bridged Networking

**Phase:** Prerequisites — VM Setup  
**Date:** <!-- fill in -->

**Symptom:**  
Ubuntu VM in VMware could not access the internet when network adapter was set to Bridged (Automatic).

**Investigation:**  
<!-- Write what you tried -->

**Root Cause:**  
<!-- Write what caused it in your own words -->

**Fix:**  
Switched VM network adapter from Bridged to NAT mode. NAT allows the VM to share the host's internet connection reliably without needing to bind to a specific physical adapter.

**Lesson Learned:**  
Bridged networking requires VMware to bind to the correct active physical adapter — it can bind to the wrong one automatically. NAT is more reliable for general internet access in a home lab environment.

---

<!-- 
ADD NEW ISSUES BELOW AS YOU ENCOUNTER THEM.
Copy this template for each new issue:

## Issue 00X — Short Description

**Phase:**  
**Date:**  

**Symptom:**  

**Investigation:**  

**Root Cause:**  

**Fix:**  

**Lesson Learned:**  

-->
