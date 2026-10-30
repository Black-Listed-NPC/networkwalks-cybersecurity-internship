# Week 1 - Project Module 1: Cybersecurity Lab Setup (WK1-PM1)

**Student Name:** Yash Kumar Kumawat  
**Intern ID:** NW-084-09R  
**Batch:** B084  
**Organization:** Networkwalks IT Solutions & Training  

---

## 📌 Objective
To build an isolated, secure Cybersecurity Testing Lab on a private subnet (`10.0.0.0/24`) configured with an attacking machine (Kali Linux) and victim machines for penetration testing and digital forensics practice.

---

## ⚙️ Network & Lab Specifications

| Parameter | Configuration |
| :--- | :--- |
| **Virtualization Platform** | VirtualBox / VMware Workstation |
| **Attacker VM** | Kali Linux |
| **Network Type** | NAT Network (`NatNetwork`) |
| **Subnet** | `10.0.0.0/24` |
| **Gateway** | `10.0.0.1` |
| **Kali Static IP** | `10.0.0.2/24` |
| **DNS Server** | `8.8.8.8` / `10.0.0.1` |
| **Shared Folder** | Host `/downloads` folder shared & auto-mounted |
| **VM Integration** | Bidirectional Clipboard & Drag-and-Drop enabled |

---

## 🚀 Execution Steps

1. **Virtual Network Setup:** Created custom NAT Network `NatNetwork` with IPv4 prefix `10.0.0.0/24` and DHCP enabled.
2. **VM Integration:** Enabled bidirectional shared clipboard, drag-and-drop, and mapped the host `/downloads` directory.
3. **Static IP Configuration:** Configured Kali Linux with manual IPv4 address `10.0.0.2`, netmask `24`, gateway `10.0.0.1`, and DNS `8.8.8.8`.
4. **Network Fix:** Executed NetworkManager commands to address DAD timeout issues:
   ```bash
   sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
   sudo nmcli connection down "Wired connection 1"
   sudo nmcli connection up "Wired connection 1"
