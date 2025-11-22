

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





