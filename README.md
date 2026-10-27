# NETWORKWALKS-Leisha-B084-WK1-PM1-CYBERSECURITY-LAB-SETUP

## Cybersecurity Lab Setup with VirtualBox and Kali Linux

**Author:** Leisha Santoshkumar
**GitHub:** [@leishaa10](https://github.com/leishaa10)
**Program:** NETWORKWALKS Cybersecurity Internship, Week 1 (WK1-PM1)

---

## 1. Overview

For Week 1 of the internship I built the base of a cybersecurity lab on my Windows laptop. Kali Linux runs as a virtual machine inside Oracle VirtualBox 7.2.22 and is attached to a custom NAT Network called `NATNetwork` (`10.0.0.0/24`), so that more machines can be added to the same network in later weeks.

This README records what I did, the screenshot that proves each step, the problems I ran into, and the items that are still pending.

## 2. Task Checklist

| Task requirement | Status | Proof |
|---|---|---|
| Use VirtualBox (latest version) | Done, version 7.2.22 | Screenshot 01, 02 |
| Set up Kali Linux as the attacking machine | Done | Screenshot 03, 04 |
| NATNetwork with subnet 10.0.0.0/24 | Done | PowerShell output (section 6), Screenshot 05 |
| Kali connected to NATNetwork | Done | Screenshot 06, 07 |
| Kali has internet access | Done, Google opened in Firefox | Screenshot 09 |
| Kali IP address 10.0.0.2/24 | **Pending** | See section 7 |
| Clipboard and drag/drop enabled | **Pending** | |
| Shared folder (Downloads) from host | **Pending** | |
| Clean snapshot | **Pending** | |

## 3. Tools Used

| Tool | Purpose |
|---|---|
| 7-Zip | Extract the Kali `.7z` archive |
| Oracle VirtualBox 7.2.22 | Run the Kali VM and create the NAT Network |
| Kali Linux 2026.3 (VirtualBox image) | Attacking machine for the lab |
| Windows PowerShell and `VBoxManage` | Create and manage the NAT Network from the command line |
| NetworkManager (`nm-connection-editor`) | Inspect the wired connection inside Kali |
| Firefox | Check internet access |

## 4. Step 1: Install 7-Zip and Extract Kali

I installed 7-Zip (`7z2605-x64.exe`) and used it to extract the downloaded `kali-linux-2026.3-virtualbox-amd64.7z` into a folder in Downloads.
<img width="782" height="77" alt="vm1main" src="https://github.com/user-attachments/assets/578d5360-5446-44e7-8094-2f6430858df4" />

The extracted folder has two main files. The `.vbox` file holds the VM configuration and the `.vdi` file is the virtual hard disk (about 16 GB).

<img width="906" height="146" alt="vm1" src="https://github.com/user-attachments/assets/839d877a-9ded-4b92-854d-81a5c42ea2e7" />

## 5. Step 2: Install VirtualBox and Add the Kali VM

I installed VirtualBox 7.2.22 and added the Kali VM using its `.vbox` file. The VM details show Debian (64-bit), 2048 MB base memory, 2 processors and an 80.09 GB disk.

<img width="882" height="155" alt="vm5" src="https://github.com/user-attachments/assets/0a80b1d2-bc24-42ee-a575-5c837cfe387a" />

## 6. Step 3: Open the Network Tool and Check NATNetwork

In VirtualBox, the network settings are opened from **File → Tools → Network** (shortcut `Ctrl + H`). This tool has three tabs: Host-only Networks, NAT Networks and Cloud Networks.

<img width="1917" height="1020" alt="vm2" src="https://github.com/user-attachments/assets/db39254c-b9aa-41b6-9555-746574f0d6d7" />


The NAT network required for this lab is:

| Setting | Value |
|---|---|
| Name | NATNetwork |
| IPv4 Prefix | 10.0.0.0/24 |
| DHCP Server | Enabled |
| IPv6 | Disabled |

I opened the **NAT Networks** tab to check that `NATNetwork` was listed with the prefix `10.0.0.0/24` and DHCP enabled.

<img width="1917" height="1012" alt="vm3" src="https://github.com/user-attachments/assets/f579a00c-89ba-4638-bf62-8a6aa7673136" />

## 7. Step 4: Attach Kali to NATNetwork

With the VM powered off, I opened Settings → Network → Adapter 1 and set:

| Setting | Value |
|---|---|
| Enable Network Adapter | Checked |
| Attached to | NAT Network |
| Name | NATNetwork |
| Adapter Type | Intel PRO/1000 MT Desktop (82540EM) |
| Promiscuous Mode | Allow All |
| Cable Connected | Checked |

<img width="1917" height="865" alt="vm4" src="https://github.com/user-attachments/assets/74c415ea-8309-4d78-a70d-beb608c91f2c" />


The VM details page also lists Adapter 1 as `NAT Network, 'NATNetwork'` (visible in the screenshot in section 6).

## 8. Step 5: Kali Network Connection and Internet Check

Inside Kali I opened the wired connection editor from the terminal:

```bash
nm-connection-editor
```

It opened "Editing Wired connection 1", with the device shown as `eth0`.

<img width="1917" height="827" alt="vm6" src="https://github.com/user-attachments/assets/d8f75f91-59e9-4d19-a842-3618a0d08219" />

I then opened Firefox and loaded `www.google.com` successfully, which shows the VM could reach the internet.

<img width="1917" height="950" alt="vm8" src="https://github.com/user-attachments/assets/f93eea38-2fd5-4dce-9072-cafd762a7b5b" />

## 9. Problems I Faced and How I Solved Them

| # | Problem | What I did |
|---|---|---|
| 1 | The VM showed as Inaccessible with a "Can't open machine" notification. It had been added from a temporary folder that no longer existed. | Removed the broken entry with **Remove Only**, extracted the archive properly with 7-Zip, and added the `.vbox` file from the extracted folder. |
| 2 | The VirtualBox installer ended prematurely during the update. I did not find the exact cause. | Restarted the PC and ran the installer again. It completed and installed 7.2.22. |
| 3 | I could not find Network under File → Tools or in Preferences. | Created the NAT Network with `VBoxManage`. Later found the tool under File → Tools → Network (`Ctrl + H`). |
| 4 | PowerShell said `VBoxManage` was not recognised, and a quoted path gave an "Unexpected token" error. | Ran it from the VirtualBox folder with `.\VBoxManage.exe`. A quoted path needs the `&` call operator. |
| 5 | `modifyvm` failed with "machine is already locked for a session". | The VM was still running. I shut it down fully and ran the command again. |
| 6 | I had first created a network called `PentestLab` (`192.168.50.0/24`) instead of the required `NATNetwork`. Removing it gave `E_INVALIDARG`, because Adapter 1 was still attached to it. | Switched Adapter 1 to `NATNetwork`, then ran `natnetwork stop` and `natnetwork remove`. The old entry stayed in the GUI for a while until it refreshed. |
| 7 | The network icon menu in Kali only showed a wireless entry, so I could not find Edit Connections. | Ran `nm-connection-editor` from the terminal. A VM only has a virtual wired adapter, so the wireless entry was not usable. |
| 8 | Host RAM usage was around 88 to 90% while the VM had only 2048 MB. | Closed other applications before starting the VM. |

## 10. What I Learned

- A VM depends on its `.vbox` and `.vdi` files staying in a permanent folder. Adding it from a temporary location breaks it later.
- NAT and NAT Network are different VirtualBox modes. A NAT Network lets several VMs share one virtual subnet.
- Network adapter settings cannot be changed while the VM is running or in a saved state.
- `VBoxManage` is useful when a GUI option is hard to find, but PowerShell needs `.\` or `&` to run it.
- Reading error messages carefully (locked session, `E_INVALIDARG`) pointed me to the cause each time.

##Project Information

| Field | Details |
|---|---|
| Project | Cybersecurity and Penetration Testing Lab Setup |
| Organisation | NETWORKWALKS |
| Week | 1 (WK1-PM1) |
| Author | Leisha Santoshkumar |
| Tools | VirtualBox 7.2.22, Kali Linux 2026.3, 7-Zip |
