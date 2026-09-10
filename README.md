# 🧪 Cybersecurity Testing Lab Setup — VirtualBox + Kali Linux

## 📌 Project Overview

This project documents the setup of a **virtual cybersecurity testing lab** on my own laptop/PC using VirtualBox and Kali Linux.

The goal was to build an isolated environment where I can safely practice network scanning, reconnaissance, and other security-testing activities without touching any real network. The lab runs on its own private NAT Network, so more machines (targets, additional attacker boxes, etc.) can be added later.

This was my first task in the course, and everything below is what I actually did to complete it — including the problem I ran into and how I fixed it.

---

## 🎯 Objectives

- Install VirtualBox as the hypervisor.
- Set up Kali Linux as the attacking/hacker machine.
- Create a private **NAT Network** on subnet `10.0.0.0/24`.
- Assign Kali a static IP of `10.0.0.2/24` on that network.
- Give Kali full internet access.
- Enable clipboard sharing and drag-and-drop between host and VM.
- Enable a shared folder (`/downloads`) between host and VM.
- Take a clean snapshot once everything was working.

---

## ⚙️ Lab Configuration

| Component            | Configuration          |
|-----------------------|-------------------------|
| Hypervisor            | VirtualBox (latest)     |
| Attack Machine        | Kali Linux (pre-built VM) |
| Virtual Network       | NAT Network — `NATNetwork` |
| Network Address       | `10.0.0.0/24`            |
| Kali IP Address       | `10.0.0.2/24`            |
| Default Gateway       | `10.0.0.1`               |
| DNS Server            | `8.8.8.8`                |
| Shared Folder         | `/downloads` (host → guest) |
| Clipboard / Drag&Drop | Enabled (bidirectional)  |

---

## 🪜 Setup Steps

### 1. Install VirtualBox
Downloaded and installed VirtualBox on the host machine.

### 2. Download the Kali Linux VM
Instead of installing from an ISO, I downloaded the pre-built Kali Linux virtual machine image so it could be imported directly into VirtualBox.

### 3. Create the NAT Network
Created a NAT Network in VirtualBox and named it **NATNetwork**, using the `10.0.0.0/24` subnet. A NAT Network was used (instead of plain NAT) so that any additional VMs added to the lab later can talk to each other while still reaching the internet.

### 4. Attach Kali to the NAT Network
Opened the Kali VM's network settings and set its adapter to use **NATNetwork**.

### 5. Configure a static IP on Kali
Edited the network connection inside Kali and switched it from automatic (DHCP) to manual, setting:
- IP address: `10.0.0.2`
- Subnet mask: `/24` (`255.255.255.0`)
- Gateway: `10.0.0.1`
- DNS: `8.8.8.8`

### 6. Fix the connection after switching to static IP
After saving the manual configuration, the connection didn't come back up on its own. I fixed it by running:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

This disabled the IPv4 duplicate-address-detection delay and then bounced the connection, which brought it back up successfully.

### 7. Verify the IP address
Confirmed with `ip a` that Kali was correctly holding `10.0.0.2/24`, and that the "Wired connection 1" showed as active.

### 8. Enable clipboard sharing and drag-and-drop
In the VM settings, enabled bidirectional clipboard and drag-and-drop between the host and the Kali VM.

### 9. Enable the shared folder
Set up a shared folder pointing at `/downloads` on the host so files can be moved between host and guest without needing the network.

### 10. Take a snapshot
Once the network, shared folder, and clipboard settings were all confirmed working, took a VirtualBox snapshot named **"first snapshot"** so this clean, working state can be restored later if a future exercise breaks something.

---

## 🐞 Problem Encountered & Fix

**Problem:** After manually configuring the static IP (`10.0.0.2/24`, gateway `10.0.0.1`, DNS `8.8.8.8`), the network connection did not re-establish on its own.

**Fix:** Ran the following to disable the IPv4 DAD timeout and restart the connection:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

After this, the connection came up successfully and `ip a` confirmed the correct `10.0.0.2` address.

> **Note:** Connection names can differ between systems (e.g. it might not be called "Wired connection 1" on another machine) — check the actual name with `nmcli connection show` before running this.

---

## 💡 What I Learned

- **NAT Network vs. plain NAT** — a NAT Network lets multiple VMs on the same virtual network talk to each other while still getting internet access, which is what makes it suitable for a multi-machine lab later on.
- **Static IP configuration in Linux** — how to set IP, subnet, gateway, and DNS manually, and how to verify it with `ip a`.
- **Troubleshooting NetworkManager with `nmcli`** — how to modify, bring down, and bring back up a connection when a manual config doesn't apply cleanly.
- **Snapshots** — why it's worth taking a clean snapshot right after a working baseline is reached, so I have something to roll back to.

---

## 🔐 Security & Ethical Use

This lab is for educational purposes only. It will only be used against systems I own or have explicit permission to test.

---

## 🔗 Tools & Resources

- **VirtualBox:** https://virtualbox.org/wiki/Downloads
- **Kali Linux:** https://kali.org/get-kali

---

## 👤 Author

**MENNA ALLAh**
Cybersecurity Student
