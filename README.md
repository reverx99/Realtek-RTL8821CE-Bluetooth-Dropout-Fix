# 📡 Realtek RTL8821CE Bluetooth Dropout Fix  


## 🔎 Overview

The **Realtek RTL8821CE** Wi-Fi + Bluetooth combo chip is notorious on Linux.  
If you're experiencing random Bluetooth audio stutters, dropouts, or fragmented playback — you're not crazy.  
It's the **driver**.

This guide documents **the exact fix** that stabilized Bluetooth audio on my machine.  
It is **not** a universal solution — only applies to **RTL8821CE (rtw88)** devices.

---

## 🚨 Issue

When using Bluetooth headphones, I was getting:

- Random micro dropouts  
- Stuttering every few seconds  
- PipeWire logs full of:
  - `transport failure`
  - `decode failed -3`
  - `out of buffers`
  - `error 24`
  - `fd0` crash events  

### 💡 Root Cause  
Realtek’s **LPS (Low Power Save)** and **ASPM (PCIe power management)** modes break Bluetooth transport on Linux.

These “power savings” introduce latency spikes and packet loss.

---

## 🛠️ The Fix 

### 1) Create a modprobe override file

```bash
sudo nano /etc/modprobe.d/realtek-bt.conf
```
Paste:
```bash
options rtw88_core disable_lps=1 disable_aspm=1
options rtw88_pci disable_aspm=1
```

Save & exit (Ctrl + X).

### 2) Rebuild initramfs
```bash
sudo dracut --force
```
This ensures the new driver parameters apply at boot.
### 3) Reboot
```bash
sudo reboot
```
