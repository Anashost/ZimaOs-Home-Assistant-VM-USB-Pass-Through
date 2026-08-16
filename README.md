<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:00fffd,100:00fffd&height=120&section=header" width="100%" />

<a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=800&size=40&pause=1000&color=00fffd&center=true&vCenter=true&width=800&lines= HOME+ASSISTANT+PASSTHROUGH+|+ZIMAOS" alt="Typing SVG" /></a>

  <p align="center">
    <i>The ultimate, copy-and-paste guide to passing through USB devices (Zigbee, Z-Wave, Bluetooth) to your Home Assistant Virtual Machine on ZimaOS.</i>
  </p>


  <a href="https://revolut.me/anas4e">
    <img src="https://img.shields.io/badge/revolut-FFFFFF?style=for-the-badge&logo=revolut&logoColor=black" alt="Revolut" />
  </a>
  <a href="https://paypal.me/anasboxsupport">
    <img src="https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white" alt="PayPal" />
  </a>
  <a href="https://ko-fi.com/anasbox">
    <img src="https://img.shields.io/badge/Ko--fi-F16061?style=for-the-badge&logo=ko-fi&logoColor=white" alt="Ko-Fi" />
  </a>
  <a href="https://www.buymeacoffee.com/anasbox">
    <img src="https://img.shields.io/badge/Buy%20Me%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black" alt="Buy Me A Coffee" />
  </a>
  <a href="https://patreon.com/AnasBox">
    <img src="https://img.shields.io/badge/patreon-404040?style=for-the-badge&logo=patreon&logoColor=white" alt="Patreon" />
  </a>
  
</div>

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🚀 Intro

So, you just spun up a Home Assistant VM on your ZimaOS rig, plugged in your shiny new USB dongle... and nothing happened? 

Don't panic. By default, USBs plugged into the host aren't visible to the Virtual Machine. This guide will show you how to "pass through" the USB from ZimaOS directly to Home Assistant using simple copy-and-paste commands. 

> [!NOTE]
> 🔐COMMAND ACCESS:
> 
> If you are logged in as the `root` user, you can run all the commands below as they are. If you are logged in as a normal user and get a "Permission Denied" error, simply type `sudo ` before the command!

### 💻 Supported Hardware
This method works seamlessly on any device running **ZimaOS**, including:
- 🧊 **ZimaBoard** (Single Board Server)
- 🗡️ **ZimaBlade** (Personal NAS / Edge Server)
- 🔲 **ZimaCube** (Personal Cloud)

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🛠️ Step 1: Find Your VM ID
First, we need to locate the unique ID of your Home Assistant Virtual Machine. 
Connect to your ZimaOS server via SSH, and type:

```bash
sudo virsh list --all
```

**What you will see:**
```text
 Id   Name       State
--------------------------
 1    8a122bae   running
```
> 📝 **Target Locked:** Write down the sequence of numbers and letters under **Name** (e.g., `8a122bae`). You will replace `<YOUR_VM_ID>` with this in the steps below!

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🔍 Step 2: Identify Your USB Stick
Next, let's find the exact digital signature of the USB device you want to pass through. Run:

```bash
lsusb
```

Look for your device (like a Sonoff stick or Bluetooth radio) in the list. It will look something like this:
`Bus 001 Device 003: ID 10c4:ea60 Silicon Labs CP210x UART Bridge`

The most important part is the **ID** (`10c4:ea60`). 
* The first part (`10c4`) is your **Vendor ID**.
* The second part (`ea60`) is your **Product ID**.

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 📝 Step 3: Create the Passthrough File
We need to tell the Virtual Machine exactly which device to grab by creating a quick `.xml` configuration file.

Open a text editor in your terminal:
```bash
sudo nano /tmp/usb-dongle.xml
```

Paste the following code into the editor. **Make sure to replace the `vendor id` and `product id` with the ones you found in Step 2!**

```xml
<hostdev mode='subsystem' type='usb'>
  <source>
    <vendor id='0x10c4'/>
    <product id='0xea60'/>
  </source>
</hostdev>
```

*(Note: Always keep the `0x` before your 4-character IDs!)*

**How to save and exit in Nano:**
1. Press <kbd>Ctrl</kbd> + <kbd>O</kbd> to save.
2. Press <kbd>Enter</kbd> to confirm the filename.
3. Press <kbd>Ctrl</kbd> + <kbd>X</kbd> to exit.

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## ⚡ Step 4: Attach the USB to Home Assistant
Now for the magic. We will inject the device into your VM permanently. Run this command (remember to swap in your actual VM Name!):

```bash
sudo virsh attach-device <YOUR_VM_ID> --file /tmp/usb-dongle.xml --persistent
```
*Example:* `sudo virsh attach-device 8a122bae --file /tmp/usb-dongle.xml --persistent`

If successful, the terminal will report: **`Device attached successfully`**. 🎉

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🧬 Step 5: Adding Multiple Devices?
Do you have a Bluetooth dongle AND a Zigbee stick? You can add as many as you want, but you must do them **one at a time**:

1. Run `lsusb` to find the new device's IDs.
2. Create a **new** file with a different name (e.g., `sudo nano /tmp/usb-bluetooth.xml`).
3. Paste the XML code, updating it with the new Vendor and Product IDs.
4. Run the attach command again, pointing to your new file:
   `sudo virsh attach-device <YOUR_VM_ID> --file /tmp/usb-bluetooth.xml --persistent`

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## ✅ Verification & Final Checks
Want to make sure the passthrough is holding strong? Run the QEMU Monitor command to ask the VM what it currently sees:

```bash
sudo virsh qemu-monitor-command <YOUR_VM_ID> --hmp "info usb"
```

If the uplink is active, you will see your devices listed as a `hostdev`:
```text
  Device 0.1, Port 1, Speed 480 Mb/s, Product QEMU USB Tablet, ID: input0
  Device 0.2, Port 2, Speed 12 Mb/s, Product Sonoff Zigbee 3.0 USB Dongle Pl, ID: hostdev0
  Device 0.3, Port 3, Speed 12 Mb/s, Product Bluetooth 5.1 Radio, ID: hostdev1
```
<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🏠 Final Step in Home Assistant
1. Open your Home Assistant Web UI.
2. Navigate to **Settings** ➡️ **System** ➡️ **Hardware**.
3. Click **All Hardware**.
4. Search for `ttyUSB` or your brand name (e.g., `Sonoff`).
5. Set up your ZHA / Zigbee2MQTT or Bluetooth integration!

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

## 🔄 Bonus: Autostart on Boot
To ensure your Home Assistant VM (and its USB passthroughs) automatically turns on whenever your Zima device reboots, run:

```bash
sudo virsh autostart <YOUR_VM_ID>
```

<img src="https://capsule-render.vercel.app/api?type=rect&color=0:00fffd,100:00FFFF&height=4" width="100%" />

<div align="center">
  <i>Did this guide upgrade your smart home? Star ⭐ this repository and share it with the Zima community!</i>
</div>

<br>

<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:00fffd,100:00fffd&height=100&section=footer" width="100%" />
  <br>
  <i><a href="https://github.com/Anashost">@Anashost</a></i>
</div>
