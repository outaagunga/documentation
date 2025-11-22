
You are correct that the **Deauthentication Attack combined with a Handshake Capture** is a primary method. However, there are several other notable attacks that ethical hackers use to reveal Wi-Fi passwords, which generally fall into three categories: **Social Engineering/Phishing**, **Protocol Exploitation**, and **Direct Cracking**.

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
