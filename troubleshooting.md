# Troubleshooting Log

A record of every significant issue encountered during this project, how it was diagnosed, and what fixed it.

---

## Issue 001 — Router DHCP Reservation Not Showing Up

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

## Issue 002 — Static IP Not Applying After Editing dhcpcd.conf

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
Used `nmcli` (NetworkManager CLI) to configure a static IP address for the Ethernet connection profile. Verified the configuration using `ip a`, confirming that the static IP `192.168.1.10` was successfully assigned to the network interface..

**Lesson Learned:**  
Always check the OS version before following network configuration guides. Many online guides still reference `dhcpcd` which is outdated for Raspberry Pi OS Bookworm. Verify the active network manager with `nmcli -v` before making changes.

---

## Issue 003 — VNC Not Connecting After Switching to Ethernet

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
