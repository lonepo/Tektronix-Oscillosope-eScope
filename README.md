# Tektronix Oscilloscope e*Scope Setup Guide (Static IP)

This guide covers two connection methods:
- **Scenario A:** Scope connected to the **wall network panel** (institutional LAN).
- **Scenario B:** Scope connected **directly** to your PC's Ethernet port (while your PC uses WiFi).

---

## Prerequisites
- Standard Ethernet cable.
- Your PC's network adapter settings must be accessible (Admin rights may be needed).
- The oscilloscope's model supports e*Scope (most Tektronix DPO/MDO/TBS series).

---

## Step 1: Physical Cable Connection
- **Scenario A (Wall):** Plug one end of the Ethernet cable into the scope's RJ-45 port. Plug the other end into the **wall network outlet** or an active **institutional network switch**.
  > *[Example: Plug into the wall port labeled "Data Port 3" near your bench.]*

- **Scenario B (Direct):** Plug one end of the Ethernet cable into the scope's RJ-45 port. Plug the other end **directly** into your **PC's Ethernet port** (the one usually used for wired internet). Keep your PC connected to WiFi for other internet access.
  > *[Example: Connect directly from the scope to the back of your desktop tower.]*

---

## Step 2: Determine the IP Settings to Use
You must choose an IP that belongs to the correct network. **Do NOT use an IP that is already taken.**

### Scenario A – Wall Plug (Institutional LAN)
1. On your desktop, open Command Prompt (Windows) or Terminal (Linux).
2. Type `ipconfig` (Windows) or `ifconfig` (Linux) and look at the adapter that connects to the institution network (either your wired Ethernet or WiFi).
3. Write down your PC's **Subnet Mask**, **Default Gateway**, and **DNS Server**.
4. The scope's IP must have the **same first three number blocks** as your PC, but a **different final block**.
5. To test if a candidate IP is free, type `ping xxx.xxx.x.xxx`. If you get **"Request timed out"** or **"unreachable"**, that IP is free. If you get a **reply**, choose another final block number and test again.
> *[Example: Your PC shows IP `192.168.20.5`, Subnet `255.255.240.0`, Gateway `192.168.16.1`. You pick `192.168.25.50`. Pinging it times out, so it is free.]*

### Scenario B – Direct Cable to PC (PC on WiFi)
Because the scope is connected *only* to your PC (and not the institution network), you can create a **private, isolated network** between them. 
1. The PC's **Ethernet port** will get a manual IP. The **Scope** will get a manual IP in the exact same private range.
2. Use the standard private range `192.168.0.x`. 
3. Choose **`192.168.0.1`** for your PC's Ethernet port.
4. Choose **`192.168.0.2`** for the Oscilloscope.
5. You do **not** need a Gateway or DNS for this direct connection (leave them blank or set to `0.0.0.0`). The Subnet Mask will be `255.255.255.0`.
6. To verify the scope IP is free: Since nothing else is on this direct cable yet, `192.168.0.2` is guaranteed free.
> *[Example: PC Ethernet = `192.168.0.1`. Scope = `192.168.0.2`. Subnet = `255.255.255.0`. Gateway = blank. DNS = blank.]*

---

## Step 3: Configure the Oscilloscope's Network Settings
1. Press the **`Utility`** button on the oscilloscope's front panel.
2. On the screen, press the soft-key for **`I/O`** (Input/Output). 
   *(Note: On some older models, navigate `Utility` > `System` > `I/O`).*
3. Select **`Ethernet`** or **`Network Configuration`**.
4. Change the mode from `Auto (DHCP)` to **`Manual`** (or `Static`).
5. Enter the following values based on your Scenario:

| Setting | Scenario A (Wall) – Generic Value | Scenario B (Direct) – Generic Value |
| :--- | :--- | :--- |
| **IP Address** | Free IP in the institution range (format `xxx.xxx.x.xxx`) | `192.168.0.2` (format `xxx.xxx.x.xxx`) |
| **Subnet Mask** | Exact same subnet as your PC (format `xxx.xxx.x.xxx`) | `255.255.255.0` |
| **Default Gateway** | Exact same gateway as your PC (format `xxx.xxx.x.xxx`) | Leave blank or enter `192.168.0.1` |
| **DNS Server** | Institution DNS (format `xxx.xxx.x.xxx`) | Leave blank or enter `8.8.8.8` (not used) |

6. Press **Apply** or **OK** to save. The scope will restart its network interface.
> *[Example A: IP=`192.168.25.50`, Subnet=`255.255.240.0`, Gateway=`192.168.16.1`, DNS=`192.168.16.10`]*
>
> *[Example B: IP=`192.168.0.2`, Subnet=`255.255.255.0`, Gateway=blank, DNS=blank]*

---

## Step 4: Configure Your Desktop PC's Network Adapter
Your PC must be configured to talk to the scope.

### Scenario A – Wall Plug
- If your PC uses **DHCP (Automatic)** – you are most likely already correctly configured. Just verify your PC's IP is **not** the same as the scope's IP.
- If your PC uses a **Manual (Static)** IP – open your network adapter settings and ensure your Subnet Mask and Gateway **exactly match** what you entered on the scope.
> *[Example: PC IP is `192.168.20.5` (different from scope `192.168.25.50`). Both share Subnet `255.255.240.0` and Gateway `192.168.16.1`. No changes needed.]*

### Scenario B – Direct Cable (Crucial Step)
You **must** manually set a static IP on your PC's Ethernet port (the one the scope is plugged into).
1. Go to your PC's **Network Adapter Settings**.
2. Find the adapter named **"Ethernet"** (do not change your WiFi adapter).
3. Select **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
4. Select **"Use the following IP address"** and enter:
   - **IP Address:** `192.168.0.1` (or `xxx.xxx.x.1` if you chose a different private range).
   - **Subnet Mask:** `255.255.255.0`
   - **Default Gateway:** Leave blank.
5. Click **OK** to save.
> *[Example: PC Ethernet port is set to IP `192.168.0.1` and Subnet `255.255.255.0`. The scope is `192.168.0.2`. They are now on the same private network.]*

---

## Step 5: Access the Scope via Browser (e*Scope)
1. On your desktop, open any web browser (Chrome, Firefox, Edge).
2. In the address bar, type `http://` followed by the **exact IP address you assigned to the oscilloscope**. Press Enter.
   - **Important:** Use `http://` (not `https://`).
3. The Tektronix **e*Scope** portal page will load.
4. Click the button labeled **"Launch e*Scope"**, **"Virtual Front Panel"**, or **"Connect"**.
5. The virtual scope interface will open in a new tab. Use your mouse to click buttons and turn knobs remotely.
> *[Example A: Type `http://192.168.25.50` into the browser.]*
>
> *[Example B: Type `http://192.168.0.2` into the browser.]*

---

## Troubleshooting (If the page does not load)
- **Firewall:** Temporarily disable Windows Defender Firewall or your Linux firewall just to test. If it works, create an inbound rule to allow traffic to/from the scope's IP.
- **Ping Test:** Open Command Prompt and type `ping [Scope_IP]`. If you get replies, the network is fine; the issue might be the browser. If you get timeouts, re-check Steps 3 and 4.
- **Cable for Direct:** If using Scenario B and the connection fails, ensure your Ethernet cable is **straight-through** (most modern ones are) or your PC supports Auto-MDI/X (almost all do). A crossover cable is rarely needed these days.
- **Reboot:** Turn the oscilloscope off, wait 10 seconds, and turn it back on to force the new IP settings to activate.
