# Router-Level Location Masking & Censorship Circumvention Guide
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

# Router-Level Location Masking & Censorship Circumvention Guide
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



