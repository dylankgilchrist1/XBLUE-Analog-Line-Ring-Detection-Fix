# Analog Line Ring Detection Fix (Windstream FXO Timing Adjustment)

This guide addresses a specific analog FXO line issue where inbound calls from Windstream do not ring when routed to a ring group.  
The issue is caused by timing mismatches and debounce behavior on the FXO port.  

**Applies to:** QB Servers    
**Last Updated:** October 2025  
**Tested Firmware:** 30.15.55.125.10 and later

## Root Cause
Windstream’s analog FXO signaling can send shorter or weaker ring voltage bursts than standard line timing expects.  
The PBX’s default debounce and timer values cause it to miss or misinterpret these short pulses, leading to:
- Missed ring events for groups or extensions
- Inconsistent Caller ID capture
- Delayed or silent ringbacks

## Solution 
Adjust the FXO module timing parameters to improve ring signal detection on analog lines, particularly when using carriers like Windstream. By setting custom debounce and timer intervals, the PBX filters out transient voltage fluctuations and interprets incoming ring signals more reliably. These parameters are applied at boot via the `/ysdisk/support/add.sh` script, ensuring stable operation and consistent ring detection for all FXO ports.


The script modifies hardware-level FXO timing parameters to match Windstream’s signaling cadence.
## Obtaining SSH Password on QB Server
1. Login to the QB Server web interface.  
2. Navigate to **Settings → Security → Service**.  
3. Enable the **SSH** checkbox.  
   - A password will appear — **save or copy** it for later.  
<img width="1400" height="816" alt="Untitled design (5)" src="https://github.com/user-attachments/assets/ae944f44-66ff-4cda-8371-2cdc09501df6" />


## Downloading PuTTY and loging in 
1. Download and open PuTTY
2. Connect using:  
   - **Host Name / IP Address:** your QB Server IP  
   - **Port:** `8022`  
   - **Connection Type:** SSH  
3. When prompted:  
   - **Username:** `support`  
   - **Password:** *(the password shown on the PBX SSH screen)*  
4. Once connected, you should see:  
   ```bash
   support@IPPBX:~$
   ```
To paste text in PuTTY, simply right-click.

---

### Step 1 — Open the Script File
```bash
vi /ysdisk/support/add.sh
```

### Step 2 — Enter Insert Mode
Press **i** to go into insert mode (you’ll see “INSERT” at the bottom of the screen).

### Step 3 — Paste the Following Commands
```bash
#!/bin/sh
echo "fxo set timer interval 24>" >/proc/module/fxo
echo "fxo set ringing debounce 15" >/proc/module/fxo
```

### Step 4 — Save and Exit
1. Press **Esc** to exit insert mode.  
2. Type:
   ```
   :wq
   ```
   and press **Enter** to save and quit.

### Step 5 — Reboot the Server
```bash
reboot
```

### Step 6 — Verify Changes
After rebooting, reopen the file to confirm your edits:
```bash
vi /ysdisk/support/add.sh
```
You should see the same lines already present.

---

### Document Control
**Title:** Analog Line Ring Detection Fix — Windstream FXO Timing Adjustment  
**Applies To:** XBLUE QB / FXO Analog Line Systems  
**Author:** Dylan Gilchrist  
**Last Updated:** October 2025  
**Version:** 1.0  

