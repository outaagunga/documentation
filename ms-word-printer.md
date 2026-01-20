
Method 1: The "No Border" Technique (Most Reliable)
1.	Click your mouse cursor in the paragraph immediately above the dark line.
2.	Go to the Home tab on the top ribbon.
3.	In the Paragraph group, click the dropdown arrow next to the Table Borders icon (looks like a small grid/window).
4.	Select No Border. The line should disappear. 




That error points to a **Windows print system issue**, not the printer itself. The HP LaserJet P2055dn is very reliable, but if the **Print Spooler service** isn’t running, *no printer will print*.

Below is a **step-by-step fix**, starting with the most common causes.

---

## Step 1: Try restarting the Print Spooler manually

1. Press **Win + R**
2. Type **services.msc** → press **Enter**
3. Find **Print Spooler**
4. Right-click → **Start**

   * If it’s running, choose **Restart**
   * If it fails or stops again, continue below

---

## Step 2: Clear stuck print jobs (very common cause)

A corrupt print job will stop the spooler from starting.

1. Press **Win + R**
2. Type:

   ```
   services.msc
   ```
3. Right-click **Print Spooler** → **Stop**
4. Open File Explorer and go to:

   ```
   C:\Windows\System32\spool\PRINTERS
   ```
5. **Delete all files** in that folder
   (Do NOT delete the folder itself)
6. Go back to **Services**
7. Right-click **Print Spooler** → **Start**

✅ If it starts and stays running, try printing again.

---

## Step 3: Set Print Spooler to start automatically

1. In **services.msc**
2. Double-click **Print Spooler**
3. Set:

   * **Startup type:** Automatic
4. Click **Apply → OK**
5. Restart the computer

---

## Step 4: Check spooler dependencies (important)

If a dependency service is stopped, the spooler won’t run.

1. Double-click **Print Spooler**
2. Go to the **Dependencies** tab
3. Make sure these are **Running**:

   * **Remote Procedure Call (RPC)**
   * **DCOM Server Process Launcher**
   * **RPC Endpoint Mapper**

If any are stopped → start them and retry the spooler.

---

## Step 5: Remove bad printer drivers (very common with older HP drivers)

The P2055dn uses older drivers that can crash the spooler.

1. Press **Win + R**
2. Type:

   ```
   printui /s /t2
   ```
3. Under **Drivers**, remove:

   * Any **HP LaserJet P2055**
   * Any duplicate or “ghost” HP printer drivers
4. Restart the computer
5. Reinstall the printer using:

   * **HP Universal Print Driver (PCL6)** ← recommended

---

## Step 6: System file check (if spooler still won’t start)

1. Open **Command Prompt as Administrator**
2. Run:

   ```
   sfc /scannow
   ```
3. Let it complete, then reboot

---

## Step 7: Test spooler without the printer

This confirms whether Windows itself is broken or the driver is.

1. Disconnect the USB / network cable from the printer
2. Start **Print Spooler**
3. If it runs fine → the **printer driver** is the problem
4. If it still fails → Windows spooler is corrupted

---

### Important questions (answering these helps narrow it fast):

* What **Windows version** are you using? (10, 11, 7?)
* Is the printer connected via **USB or network (Ethernet)**?
* Does the spooler **start then stop**, or **refuse to start at all**?
* Do you see **Event Viewer errors** mentioning `spoolsv.exe`?

Once I know that, I can give you the **exact fix** (including the correct driver and port settings for the P2055dn).
