# 1 Router-Level Location Masking & Censorship Circumvention Guide
### (GL.iNet / OpenWrt — fine-tuned for DPI evasion and leak protection)

**Read this first:** This setup masks your *network location*. It does not protect your identity if any account you use is tied to your real name, phone number, payment method, or writing style your government already associates with you. Treat network masking and identity separation as two separate problems — both matter.

---

## Step 1 — Prevent Hardware Leaks (Kill Switch + IPv6)

1. Connect to your GL.iNet router admin dashboard (typically `192.168.8.1`).
2. Go to **VPN → VPN Dashboard**.
3. Toggle on **Global Settings**.
4. Enable **Block Non-VPN Traffic** (kill switch) — if the tunnel drops, devices lose internet rather than falling back to your real IP.
5. **New:** Go to **Network → WAN** (or your relevant WAN interface) and **disable IPv6**. Also block IPv6 in the firewall rules.
   - *Why:* If your ISP hands out IPv6 and your tunnel only routes IPv4, your real location can leak straight past the kill switch over IPv6. This is one of the most common real-world deanonymization leaks and is missed by most guides.

---

## Step 2 — Set Up an Encrypted, DPI-Resistant Tunnel

**Do not rely on a bare SOCKS5 proxy.** SOCKS5 has no built-in encryption — a government-grade firewall doing deep packet inspection can often still see and flag it as proxy traffic, even if the exit IP looks like it's in the US. You need a protocol built to disguise your traffic as ordinary HTTPS.

1. Choose a provider offering **Shadowsocks, V2Ray/VMess, or Trojan** configurations — not just raw IP/port/user/pass SOCKS5 credentials. These protocols are purpose-built to <cite index="11-1">enhance privacy and bypass censorship, support multiple tunneling protocols, and obfuscate traffic to mimic HTTPS or WebSocket</cite> traffic, which is what actually defeats DPI.
2. On the GL.iNet dashboard: **Applications → Plug-ins**, search for and install **`luci-app-passwall`** (note the correct name — not "lucid-app-passwall").
3. Import your server config — usually a share-link or JSON from your provider — into Passwall, rather than typing in a raw SOCKS5 credential.
4. Set Passwall's routing mode to send **all** traffic through the tunnel (not a "bypass list" / split-tunnel mode) so nothing accidentally goes direct.
5. **Trust note:** Whoever runs your exit server can see your real IP and traffic metadata. Prefer a provider based outside your government's legal reach, with a clear no-log policy. Treat this as risk reduction, not a guarantee — a proxy/VPN provider is still a single point of trust.

---

## Step 3 — DNS Leak Protection

1. Go to **Network → DNS**.
2. Enable **DNS Rebinding Attack Protection**.
3. Enable **Encrypted DNS (DNS over TLS)**, pointed at a neutral provider (Cloudflare 1.1.1.1 or Google 8.8.8.8).
4. **New:** Make sure DNS queries are routed **through** the Passwall tunnel, not sent encrypted-but-direct over your ISP connection. Repeated direct queries to a foreign DNS resolver are themselves a metadata signal to a watching ISP — route DNS through the tunnel like everything else.

---

## Step 4 — Browser-Level Hardening (new — do this on every device, the router can't fix these)

Router-level masking does **not** stop these two common leaks:

- **WebRTC leak:** browsers can reveal a real local/public IP via STUN requests, bypassing your tunnel entirely.
  - In Firefox: `about:config` → set `media.peerconnection.enabled` to `false`, or use a WebRTC-leak-blocking extension.
- **Fingerprinting:** timezone, system language, fonts, and screen size can single you out even with a perfectly masked IP.
  - Set your device's system timezone and language to match your masked location.
  - Use Firefox with `privacy.resistFingerprinting` enabled, or the **Tor Browser** for your most sensitive advocacy sessions specifically.

---

## Step 5 — Router Hardening (new)

- Change the router's default admin password immediately.
- Disable remote/WAN-side admin access.
- Disable UPnP.
- Use WPA3 (or WPA2 if WPA3 unsupported) with a strong Wi-Fi passphrase.
- Keep router firmware updated.

---

## Final Verification Checklist

Connect a device to the router's Wi-Fi and check:

- [ ] **IPv4** leak test at browserleaks.com / ipleak.net — shows only your masked location
- [ ] **IPv6** leak test (check separately — many tools test it independently of IPv4) — should show no IPv6 address, or one matching the masked location
- [ ] **WebRTC** address — should not reveal your real IP
- [ ] **DNS servers** — should show only your tunnel/DNS-over-TLS provider, never your real ISP
- [ ] **Browser fingerprint uniqueness** at amiunique.org — a masked IP with a highly unique fingerprint can still single you out over time
- [ ] System **timezone/language** match the masked location

---

## The One Thing This Guide Can't Fix

Network masking protects your *connection*. It does not protect *who you are once logged in*. If your advocacy accounts are meant to be pseudonymous, keep them fully separate — separate device or browser profile, separate email, separate phone number, no shared payment methods, and be conscious that writing style and posting-time patterns can be their own fingerprint over time.

---

*For technical or emergency digital security support: Access Now Digital Security Helpline — help@accessnow.org (24/7). For broader protection support: Front Line Defenders — +353-1-212-3750 / info@frontlinedefenders.org.*

---
---
--- 

# 2 Router-Level Location Masking & Censorship Circumvention Guide
### (MikroTik RouterOS — fine-tuned for DPI evasion and leak protection)

**Read this first:** MikroTik RouterOS is architecturally different from OpenWrt/GL.iNet — it has no Passwall-style plugin, and no native Shadowsocks/V2Ray support. You can still build a solid, leak-proof setup, but genuine DPI evasion (making your tunnel invisible to a government firewall, not just IP-masked) requires an extra piece described in Step 2B. Read that section even if you're tempted to stop at 2A.

Network masking protects your *connection*. It does not protect *who you are once logged in* — keep pseudonymous accounts fully separate from your real identity, device fingerprints, phone numbers, and payment methods.

---

## Step 1 — Kill Switch + IPv6 Lockdown

**Kill switch (fail-closed routing):**
1. Set your VPN/tunnel interface (e.g. `wireguard1`) as the only path out, with a lower routing distance than your normal WAN route.
2. In **IP → Firewall → Filter Rules**, add a rule near the top of the `forward` chain:
   - `chain=forward out-interface=!wireguard1 dst-address-list=!vpn-endpoint action=drop`
   - This drops all forwarded traffic that isn't going out through the tunnel — except the tunnel's own handshake traffic to your VPN endpoint (add that endpoint IP to an address list called `vpn-endpoint` so the handshake itself isn't blocked).
3. Test it: disable the WireGuard interface and confirm LAN devices immediately lose internet rather than falling back to the raw WAN.

**Disable IPv6:**
1. `IPv6 → Settings` → set **Disable IPv6 = yes** (or remove the IPv6 package entirely under System → Packages if you never use it).
2. Add an IPv6 firewall filter rule: `chain=forward action=drop` (drop everything) as a backstop in case IPv6 gets re-enabled by a firmware update.
   - *Why this matters:* if your ISP hands out IPv6 and your tunnel only carries IPv4, your real location leaks straight past the kill switch over IPv6. This is one of the most common real-world leaks and easy to miss on RouterOS specifically, since IPv6 is on by default.

---

## Step 2A — Base Tunnel: Native WireGuard

RouterOS 7+ has WireGuard built in — use it as your encrypted transport layer.

1. `Interfaces → WireGuard → Add`, generate keys, set listen port.
2. Add a peer for your VPN/proxy provider (or your own VPS — see Step 2B) with their public key, endpoint, and allowed-IPs `0.0.0.0/0`.
3. `IP → Addresses` → assign the tunnel's internal IP to the WireGuard interface.
4. `IP → Routes` → add default route via the WireGuard interface at a lower distance than your WAN default route.
5. `IP → Firewall → NAT` → masquerade traffic leaving the WireGuard interface.

**Limitation to be aware of:** WireGuard is fast and well-encrypted, but its packet structure has a recognizable signature. A sophisticated DPI system can sometimes fingerprint "this is WireGuard traffic" even without decrypting it, and some government firewalls actively block or throttle detected WireGuard/OpenVPN handshakes. If you're in an environment doing active protocol-level blocking (not just IP-based blocking), continue to Step 2B.

## Step 2B — Real DPI Evasion (Shadowsocks / V2Ray / Trojan)

RouterOS has no native equivalent to Passwall. Two realistic ways to get obfuscated, DPI-resistant traffic on a MikroTik network:

**Option 1 — RouterOS container (if your hardware supports it):**
- Requires an ARM64 or x86 MikroTik device (e.g. RB5009, CCR2004, hAP ax3, or CHR on a VPS) with the **container package** enabled.
- `System → Packages` → enable the container feature, then pull a `sing-box` or `v2ray` container image, mount a config file with your Shadowsocks/V2Ray/Trojan credentials, and route LAN traffic to it via policy routing (see below).
- This is the closest MikroTik equivalent to Passwall, but setup is more manual and not all MikroTik models support containers — check your model's architecture first.

**Option 2 — External proxy box (works on any MikroTik, more reliable):**
- Run V2Ray, Shadowsocks, or sing-box as a client on a small separate device on your LAN (a Raspberry Pi or old laptop works fine), connecting out to a VPS you control running the matching server — or to a commercial provider offering these protocols.
- On the MikroTik, use **policy-based routing** (`IP → Firewall → Mangle` with `new-routing-mark`, matched to a custom routing table pointing at that device's LAN IP as gateway) to force all LAN client traffic through that box instead of directly out the WAN.
- Keep the Step 1 kill-switch logic pointed at *that* path — traffic should only be allowed out via the proxy box, never falling back to raw WAN.

Either option gives you traffic that's shaped to look like ordinary HTTPS/WebSocket traffic rather than an obvious VPN handshake — that's what actually defeats DPI-based blocking, as opposed to just IP-based blocking.

---

## Step 3 — DNS Leak Protection

1. `IP → DNS → Settings`:
   - Set servers to your VPN/proxy provider's DNS, or a neutral provider (1.1.1.1 / 8.8.8.8).
   - Enable **Use DoH Server** (DNS-over-HTTPS) if running RouterOS 7.x — set `use-doh-server` to an HTTPS DoH endpoint and enable `verify-doh-cert`.
2. **Route DNS through the tunnel, not around it:** add a firewall NAT/mangle rule so port 53 (and the DoH HTTPS request) is forced out through the WireGuard/proxy interface, not the raw WAN — otherwise your ISP still sees repeated encrypted-DNS queries leaving directly, which is itself a metadata signal even if the query content is hidden.
3. Disable `allow-remote-requests` unless you specifically need the router to serve DNS to other networks.

---

## Step 4 — Browser-Level Hardening (unrelated to router, but essential — do this per device)

- **WebRTC leak:** browsers can reveal your real local/public IP via STUN, bypassing the tunnel entirely. In Firefox, set `media.peerconnection.enabled` to `false` in `about:config`, or use a WebRTC-blocking extension.
- **Fingerprinting:** timezone, language, fonts, and screen size can identify you even with a perfectly masked IP. Match system timezone/language to your masked location; use Firefox with `privacy.resistFingerprinting` enabled, or Tor Browser for your most sensitive sessions.

---

## Step 5 — Router Hardening

- Change the default `admin` username/password immediately (`System → Users`).
- Disable Winbox, API, Telnet, and FTP access from the WAN side — restrict management to LAN only, ideally to a specific admin IP via firewall rule.
- Disable MNDP/CDP neighbor discovery on the WAN interface (`IP → Neighbors → Discovery Settings`).
- Use `www-ssl` instead of plain `www` for web admin; disable plain HTTP admin entirely.
- Keep RouterOS firmware updated (`System → Packages → Check for Updates`).
- Set a strong Wi-Fi passphrase (WPA3 if your AP hardware supports it).

---

## Final Verification Checklist

From a device on the LAN, check:

- [ ] **IPv4 leak test** (browserleaks.com / ipleak.net) — shows only masked location
- [ ] **IPv6 leak test** (checked separately from IPv4) — should show nothing, or match masked location
- [ ] **WebRTC address** — should not reveal your real IP
- [ ] **DNS servers** — should show only your tunnel/DoH provider, never your ISP's resolver
- [ ] **Browser fingerprint uniqueness** at amiunique.org
- [ ] System **timezone/language** match the masked location
- [ ] Pull the WireGuard/proxy cable/interface down and confirm the kill switch actually cuts internet rather than failing open

---

## The One Thing This Guide Can't Fix

If the account you're posting from is tied to your real identity, phone number, writing style, or posting-time patterns your government already associates with you, network masking alone won't protect you. Keep pseudonymous accounts fully separate — separate device/browser profile, separate email, separate number, no shared payment methods.

---

*For technical or emergency digital security support: Access Now Digital Security Helpline — help@accessnow.org (24/7). For broader protection support: Front Line Defenders — +353-1-212-3750 / info@frontlinedefenders.org.*

---
---
---
# 3 how websites detect user locations to apply geographic restrictions and the steps required to bypass these controls  
(http://www.youtube.com/watch?v=HgZw0q_I7Ok)

---

### Step-by-Step Overview of Geoblocking & Bypassing Methods

1. **IP Geolocation Detection (Primary Block)**
* **How it works:** Websites cross-reference your IP address against geolocation databases to identify your physical country or region.  
* **Impact:** Used by streaming platforms, e-commerce stores (price discrimination), and news outlets  
* **How to Bypass:** Route internet traffic through a Virtual Private Network (VPN) or proxy to mask your original IP address  

2. **DNS Resolution & Region Binding**
* **How it works:** Regional DNS servers route users to geographically restricted server instances or content libraries based on location  
* **How to Bypass:** Configure custom or Smart DNS settings on your network adapters  
* *Note:* Some smart TVs and streaming devices (e.g., Roku, Chromecast) hardcode DNS servers into their firmware, making simple DNS overrides ineffective  

3. **Account Region Lock**
* **How it works:** Digital platforms (such as gaming storefronts like Steam) lock accounts to the specific region in which they were created  
* **How to Bypass:** Register a new account while connected to a server in the target country via Proxy or VPN  

4. **Financial & Payment Verification**
* **How it works:** Content platforms verify whether credit cards, billing addresses, or local banking details match the target region  
* **How to Bypass:** Use region-specific virtual credit cards, local gift cards, or local payment methods on top of a VPN connection  

5. **Mobile-Specific Location Controls**  
* **How it works:** Mobile operating systems verify user locations using SIM Mobile Country Codes (MCC/MNC) and GPS/HTML5 location APIs  
* **How to Bypass:** Disable location permissions for specific applications or spoof GPS coordinates via system tools  

6. **Billing address verification**  
* **How it works:** Most websites ....    
* **How to Bypass:** Ensure you .....
  
7. **Browser language, Time Zone and Date detection**  
* **How it works:** Most websites ....    
* **How to Bypass:** Ensure you .....

---
---
---

To access Handshake (especially the Handshake AI platform or standard [U.S. Career Network](https://joinhandshake.com/)) from an unsupported region like Africa or Europe, standard commercial VPNs (like NordVPN or ExpressVPN) will usually fail.   
Handshake actively blocks standard datacenter VPN IP addresses and displays an error message stating: "Access is restricted due to VPN or proxy".   
To successfully bypass Handshake's advanced region filtering, you must align your browser fingerprint, IP type, and account credentials.  

## 1. Upgrade to a Residential Proxy (Not a Standard VPN)  
Handshake easily detects and blocks commercial VPN subnets. Instead, you need a tool that routes your connection through a legitimate home internet service provider (ISP) in the United States.   

* Static Residential Proxies: Purchase a dedicated, static U.S. residential proxy from providers like [Node Maven](https://www.instagram.com/p/Dbk4E1pqv4m/?hl=ar) or similar services.   This assigns you a clean, unflagged residential IP address that mimics a home user.     
* VPS or RDP: Set up a Virtual Private Server (VPS) or Remote Desktop Protocol (RDP) hosted in the U.S. to load a physical computer interface located in the target region.  
* AnyDesk Link: If you have a trusted contact living inside the United States, accessing their system via AnyDesk or TeamViewer is the most foolproof method.  

## 2. Match Your Device Settings  
Handshake identifies "timezone drift" where your IP says you are in the U.S. but your device clock says otherwise.  

* Change System Time: Manually set your computer or phone's system time zone to match the exact U.S. state/location of your proxy or VPS.   
* Clear Browser Cache: Clear your cache and cookies completely, or always access the website using an Incognito / Private window to clear localized tracking fragments.   
* Disable WebRTC: WebRTC leaks your true, local IP address even behind a proxy. Install a browser extension like WebRTC Control to block these leaks.   

## 3. Clear the Verification Bottlenecks  
Bypassing the website's initial geo-block is only the first step. Handshake's strict account infrastructure requires further regional validation to stay active:  

* U.S. Phone Verification: Handshake requires a mobile number to sign up and sends a one-time SMS verification code. VOIP numbers (like Google Voice) are frequently flagged, meaning you will need a real U.S. cell phone SIM card or a premium non-VOIP text-receiving service.  
* Identity Checks: Handshake AI programs request an official U.S. photo identity document (such as a U.S. Driver's License) to clear background and identity credentials. Without valid matching documentation, accounts are typically flagged and suspended.   

---
---
---

# Complete Location & Identity Protection Guide
### Network layer + Identity layer + Device layer, for advocacy work under state surveillance risk

**Core principle:** State-level tracking works across *four separate layers*. Fixing only one (network) while ignoring the others gives a false sense of security — the weakest layer gives you away regardless of how good the rest are.

| Layer | What it catches | Fixed by |
|---|---|---|
| Network | IP geolocation, DNS | Router tunnel (your existing guide) |
| Device/browser | Fingerprint, timezone, WebRTC, HTML5 geolocation | This guide, Sections 1–2 |
| Mobile hardware | GPS, cell tower, carrier data | This guide, Section 3 |
| Identity/money | Payment records, billing address, account linkage | This guide, Section 4 |

---

## Section 1 — Browser & Device Fingerprint Hardening

*Your router tunnel masks IP/DNS. None of this section is fixed by the router — do it per device.*

1. **Timezone & language:** Set your OS and browser locale/timezone to match your masked exit location. A US IP with an EAT system clock is a visible mismatch.
2. **WebRTC:** Disable it or block leaks.
   - Firefox: `about:config` → `media.peerconnection.enabled` → `false`
   - Or install a dedicated WebRTC-leak-blocking extension.
3. **HTML5 Geolocation API:** This is separate from your IP. Browsers can report actual GPS/Wi-Fi-positioning coordinates to any website that asks, *regardless of your VPN*.
   - In browser settings, set Location permission to **"Ask" or "Block"** for all sites, and deny it for anything you don't explicitly trust.
   - In Firefox you can go further: `about:config` → `geo.enabled` → `false` disables the API entirely.
4. **Use a hardened browser for sensitive work:**
   - Firefox with `privacy.resistFingerprinting` enabled, or
   - **Tor Browser** for your highest-risk sessions — it standardizes fingerprints across all its users, which defeats unique-fingerprint tracking in a way a VPN alone cannot.
5. **Check your exposure** at `amiunique.org` and `browserleaks.com` — a masked IP with a highly unique fingerprint can still single you out over repeated visits, even without ever revealing your real IP.
6. **Cookies/storage:** Use a separate browser profile (or container tabs) exclusively for advocacy accounts — never mixed with personal browsing, so tracking cookies/fingerprints can't link the two identities.

---

## Section 2 — Mobile-Specific Tracking (router masking does not reach this at all)

Your phone has three independent ways to reveal location that have **nothing to do with your Wi-Fi/router setup**:

**Cellular network / carrier:**
- If your phone is on mobile data, your carrier knows your location via cell-tower triangulation continuously, regardless of any VPN. A VPN encrypts *content*, not the fact that your SIM is pinging towers in a specific place.
- **Mitigation:** For your most sensitive sessions, put the phone in airplane mode and use Wi-Fi only (through your protected router). If you need a phone that's genuinely separate from your identity, consider a dedicated device with a prepaid/anonymous SIM used only for advocacy, never carried alongside or powered on near your personal phone.

**GPS:**
- A hardware radio, entirely independent of network routing. Any app or browser with location permission granted can read it directly.
- **Mitigation:** Disable location services at the OS level (Settings → Privacy → Location) for advocacy-related browsers/apps, or disable GPS system-wide when doing sensitive work.

**HTML5 Geolocation (browser/app level):**
- Can pull from GPS, nearby Wi-Fi SSIDs, or cell tower data — none of which route through your tunnel.
- **Mitigation:** covered in Section 1.4 above — block or ask-first location permissions.

**Practical setup for sensitive mobile use:**
- Use a device that stays on Wi-Fi only, with the SIM removed or the device kept permanently in airplane mode, for advocacy accounts.
- Keep this device physically separate from your personal phone — don't power both on in the same location repeatedly, since co-location patterns can link the two identities even without either device being individually compromised.

---

## Section 3 — Payment & Identity Separation (network masking does nothing here)

This is the layer most guides skip, and it's often the one that actually unmasks people.

**The problem:** Card networks verify the *issuing bank's country*, not your IP. Address Verification Systems (AVS) check billing address against the card issuer's records. Neither of these cares what your VPN says.

**Mitigations, roughly in order of strength:**
1. **Never reuse a card, email, or phone number** between your real identity and your advocacy identity/accounts. This is the single most important rule — one shared data point can collapse the entire separation.
2. **Payment methods that don't tie to your real billing address:**
   - Privacy-focused prepaid cards purchased with cash, where available.
   - Cryptocurrency, ideally with privacy-preserving practices (new address per transaction, not reused), for services that accept it — understanding that most crypto is traceable on-chain without additional precautions, so this is not automatically anonymous.
3. **Separate email identity:** a dedicated email (via a privacy-respecting provider) used only for advocacy accounts, never linked to your personal email as a recovery address.
4. **Separate phone number:** if any service requires SMS verification, use a number not tied to your personal identity/SIM — not your daily-use number.
5. **Billing address:** if a service requires one, do not use your real home address. Consider what's realistically available to you locally, understanding no option here is perfect — the goal is breaking the direct link between "this account" and "this specific home."

**A hard truth:** full financial anonymity against a well-resourced state adversary is very difficult. The realistic goal is to make the linkage cost the adversary significant effort and multiple independent confirmations, rather than a single database lookup.

---

## Section 4 — Account & Behavioral OPSEC

Even with every technical layer perfect, these can still identify you:

- **Writing style / stylometry:** distinctive phrasing, recurring typos, or sentence patterns can be matched across a "real" account and a pseudonymous one. Vary style consciously if this matters for your safety, or have trusted allies help draft/edit.
- **Posting time patterns:** consistently posting during your real local waking hours, even under a masked IP with a different claimed timezone, is a detectable pattern over time.
- **Cross-posting/reposting:** sharing the same content, near-simultaneously, across your real and pseudonymous accounts is one of the most common ways activists get correlated.
- **Photos/media metadata:** strip EXIF data (location, device ID) before posting any image. Be aware backgrounds in photos (landmarks, license plates, storefronts) can geolocate you even with metadata stripped.
- **Never log into a pseudonymous account from a device also logged into your real accounts**, even once — session/cookie correlation and shared device fingerprints can link them permanently.

---

## Putting It All Together — Quick Reference

| Before any sensitive session, check: | |
|---|---|
| ☐ Router tunnel + kill switch active | Network layer |
| ☐ IPv4/IPv6/DNS/WebRTC leak tests clean | Network + device layer |
| ☐ Timezone/language matches masked location | Device layer |
| ☐ Location permissions blocked in browser/OS | Device + mobile layer |
| ☐ Phone on Wi-Fi only / GPS off, or using dedicated device | Mobile layer |
| ☐ No shared card/email/phone with real identity | Identity layer |
| ☐ Separate browser profile, never logged into personal accounts | Identity layer |
| ☐ Photos stripped of metadata before posting | Behavioral layer |

---

## The Honest Limit of All of This

Perfect operational security against a well-resourced state adversary is extremely difficult to sustain indefinitely — the goal is to raise the cost and reduce the number of easy, single-point failures, not to claim invulnerability. Pair this technical work with the human-side protections already in your security plan (trusted contacts, check-in protocol, legal support) — technical anonymity buys you safety margin, but it isn't a substitute for people who will notice and act if something goes wrong.

*For technical help specific to your situation: Access Now Digital Security Helpline — help@accessnow.org (24/7, free). For broader protection support: Front Line Defenders — +353-1-212-3750 / info@frontlinedefenders.org.*


# Your Complete Protection Guide
### Plain-language, step-by-step — for staying safe while doing advocacy work under state surveillance risk

---

## How to Use This Guide

Security work has two speeds. Don't try to do everything at once — burnout leads to shortcuts, and shortcuts get people caught.

- 🔴 **TIER 1 — Critical Setup (do this within the next 1–2 weeks, once):** the foundation. Nothing else matters much if these aren't in place.
- 🟡 **TIER 2 — Daily/Weekly Habits (ongoing):** small actions that keep the foundation from leaking over time.
- 🟢 **TIER 3 — Advanced/Situational (when needed):** for your highest-risk moments specifically.

Each step tells you: **What** it is, **Why** it protects you, and **How** to actually do it.

---

## 🔴 TIER 1 — CRITICAL ONE-TIME SETUP

### 1. Get Tails Running on a USB Drive
**What:** A USB-bootable operating system that leaves no trace and routes everything through Tor.
**Why:** Your normal Windows/Mac already has months of history, cached logins, and identifying files. Tails sidesteps all of that — nothing is saved unless you explicitly choose to.

**How:**
1. Buy a USB drive, 8GB minimum, with cash if possible.
2. On your *current* computer, go to **tails.net** — type the address directly, don't search for it, to avoid a fake lookalike site.
3. **⚠️ Missing from your draft — do not skip this:** After downloading, **verify the download** using the verification tool Tails provides on their download page. A tampered/fake Tails image is a realistic attack vector against exactly the kind of person reading this guide. The official site walks you through this in a few clicks — don't install it unverified.
4. Download **BalenaEtcher** (balena.io/etcher) to write the image to your USB.
5. Plug in the USB, open BalenaEtcher, select the Tails file, select your USB, click Flash.
6. Restart your computer. As it powers on, repeatedly tap the boot menu key for your machine (usually `F12`, `F11`, `Esc`, or `Option` on Intel Macs) and select the USB drive.
7. **Optional but recommended — Persistent Storage:** Tails lets you set up one *encrypted* section of the USB that survives reboots (for saving contacts, documents, Signal data). Without it, literally everything resets every time — which is safer but less practical for ongoing work. Set a strong, unique passphrase for this if you enable it.

**Use Tails specifically for:** drafting sensitive statements, communicating with high-risk contacts, researching things you don't want linked to your identity.
**Don't rely on Tails for:** everyday advocacy on social media where you need a persistent, recognizable pseudonymous account — a persistent browser profile (Section 4) is more practical for that.

---

### 2. Encrypt Your Everyday Devices
**What:** Full-disk encryption so a seized or stolen device is unreadable without your password.
**Why:** Your router/Tails setup protects your *connection*. If someone physically takes your laptop or phone, encryption is what protects everything stored on it.

| Device | How |
|---|---|
| **Windows** | Settings → Privacy & Security → Device Encryption (or BitLocker on Pro editions) → turn on. |
| **Mac** | System Settings → Privacy & Security → FileVault → Turn On. |
| **iPhone** | Encryption is automatic once you set a passcode. Settings → Face ID/Touch ID & Passcode → set a passcode (not just 4 digits — use a longer alphanumeric one if possible). |
| **Android** | Usually on by default with a screen lock set. Settings → Security → Encryption (wording varies by manufacturer) — confirm it's active, and set a strong PIN/password, not a swipe pattern. |

**Also do:** Set your device to auto-lock after 30 seconds to 1 minute of inactivity.

---

### 3. Set Up Your Router (GL.iNet or MikroTik)
Use the router-specific guide already built for you: kill switch, IPv6 disabled, encrypted DPI-resistant tunnel (Shadowsocks/V2Ray/WireGuard), DNS routed through the tunnel. This protects your home network location.

**⚠️ Gap to flag:** The router only protects devices connected to *that* Wi-Fi. The moment your phone leaves the house and uses mobile data or another Wi-Fi network, the router does nothing. You need a **VPN app installed directly on your phone** for protection outside the house — same VPN/tunnel protocol, different point of setup.

---

### 4. Separate Your Identity Completely
**What:** A parallel set of accounts/tools that never touch your real identity.
**Why:** This is the layer most people skip, and it's the one that most often actually unmasks activists — not bad IP masking, but one shared email or card between "real you" and "advocacy you."

**How — build this stack once, keep it separate forever:**
- **Email:** one new address, privacy-respecting provider, never used for anything else, never listed as a recovery email anywhere personal.
- **Phone number:** a number not tied to your personal ID/SIM, used only for advocacy account verification.
- **Payment:** if you must pay for anything (VPN, domain), use a prepaid card bought with cash, or cryptocurrency — never a card issued in your name for anything advocacy-related.
- **Browser profile:** a separate Firefox profile or dedicated browser used *only* for advocacy accounts — never logged into personal accounts in that same profile, ever.
- **Photos:** never reuse a real photo of yourself, and strip metadata (see Tier 2) from anything you post.

---

## 🟡 TIER 2 — DAILY / WEEKLY HABITS

### Messaging & Accounts
- ✅ Use **Signal** for anything sensitive. Enable disappearing messages (Settings → Disappearing Messages, set to a short window like 1 day).
- ✅ Turn off notification previews (Signal Settings → Notifications → uncheck "show name/message") so a glance at your lock screen doesn't reveal contents.
- ✅ Verify Signal safety numbers with your most important contacts at least once (Signal will show you how) — this confirms no one is intercepting your messages in the middle.
- ✅ Use an authenticator app (not SMS) for two-factor authentication on every account — SIM-swap attacks make SMS codes interceptable.

### Browser Hygiene (every session)
- ✅ Check that your system clock/timezone matches your VPN's exit location.
- ✅ Location permission set to "Ask" or "Block" for all sites (Settings → Privacy → Location, in both browser and OS).
- ✅ For your advocacy browser profile: WebRTC disabled (Firefox: `about:config` → `media.peerconnection.enabled` → `false`).

### Photos & Media
- ✅ Strip EXIF metadata before posting any photo. On iPhone: share via Files app and "remove location data" if prompted, or use a dedicated app like ExifCleaner (desktop) before uploading. On Android: many share sheets have a "remove location" toggle — check before sending.
- ✅ Look at the actual photo content — landmarks, license plates, storefronts, school uniforms in the background can geolocate you even with metadata gone.

### Location Services
- ✅ Keep GPS/location services off system-wide except when you specifically need navigation (Settings → Privacy → Location Services, toggle per-app or entirely).
- ✅ For sensitive meetings: power the phone **fully off** (not just airplane mode) or leave it at home — airplane mode doesn't guarantee every radio is actually off on every device.

### Writing & Posting Patterns
- ✅ Vary your posting times occasionally — a consistent schedule matching your real waking hours is a pattern, even under a masked IP.
- ✅ Don't cross-post identical content near-simultaneously across your real and pseudonymous accounts.

---

## 🟢 TIER 3 — ADVANCED / SITUATIONAL

### Before a High-Risk Meeting or Sensitive Action
- Leave your personal phone at home, or put it in a **Faraday bag** (blocks all signal — GPS, cellular, Wi-Fi, Bluetooth) if you must carry it.
- Use Tails on a laptop for any drafting or communication tied to the specific action.
- Confirm your check-in protocol (from your personal security plan) is active with a trusted contact before you go.

### If You Suspect Device Compromise
- Don't try to self-diagnose spyware — this can tip off an attacker that you know.
- Contact **Access Now's Digital Security Helpline** (help@accessnow.org, 24/7, free) — they can do this safely.
- In the meantime, avoid logging into sensitive accounts from that device.

### Dedicated "Advocacy-Only" Device
If your risk level justifies it: a second phone, Wi-Fi only, SIM removed or never inserted, used exclusively for advocacy accounts, kept physically separate from your personal phone (don't power both on in the same room repeatedly — co-location patterns can link them).

---

## Full Gaps Identified & Fixed in This Version

Your original draft was strong on network-level masking. These were missing or needed correction:

1. **No verification step for the Tails download** — a compromised image is a realistic attack against high-risk users; now included.
2. **No mention of full-disk encryption** on the actual host devices — network masking doesn't help if a seized laptop's hard drive is readable.
3. **No mobile-specific VPN** — the router only covers home Wi-Fi; phones need their own VPN app for use outside the house.
4. **No 2FA guidance** — SMS-based codes are vulnerable to SIM-swap attacks; authenticator apps are safer.
5. **No screen-lock / notification-preview hardening** — a glance at a lock screen can expose sensitive Signal messages even with great encryption underneath.
6. **No safety number verification for Signal** — encryption alone doesn't stop a middle-of-conversation interception without this manual check.
7. **"Airplane mode" treated as equivalent to "off"** — it isn't a hardware guarantee. Full power-off or a Faraday bag is stronger for high-risk moments.
8. **No photo/EXIF guidance** — a single unstripped image can undo everything else in this guide.
9. **No Persistent Storage note for Tails** — without it, useful data doesn't survive reboots, which is safe but often impractical; now flagged as an explicit choice.

---

## One Honest Reminder

No combination of tools makes you invisible to a well-resourced state adversary forever. The realistic goal is to make tracking you take significant, sustained effort rather than a single easy lookup — and to pair all of this with the human side of your safety: trusted contacts, a check-in protocol, and a lawyer who can act quickly if something goes wrong.

*Free, 24/7 technical help: Access Now Digital Security Helpline — help@accessnow.org*
*Broader protection support: Front Line Defenders — +353-1-212-3750 / info@frontlinedefenders.org*

