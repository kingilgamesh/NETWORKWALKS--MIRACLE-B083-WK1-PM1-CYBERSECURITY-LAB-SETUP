# 🛡️ NETWORKWALKS - WEEK 1: CYBERSECURITY LAB SETUP

**Program:** Cybersecurity Internship @ Networkwalks  
**Author:** Orifa Miracle Oyimafu  
**Module:** WK1-PM1 - Cybersecurity & Pentesting Lab Setup  
**Instructor:** Sir Waqas Karim (CCIE)  

---

## 📌 Project Overview
The objective of this project is to build an isolated, secure, and reproducible penetration testing virtualization lab using **Oracle VM VirtualBox** and **Kali Linux**. This environment enables safe ethical hacking exercises, network configuration, and vulnerability testing without exposing host networks.

---

## 🏗️ Lab Architecture & Network Specifications

* **Hypervisor Platform:** Oracle VM VirtualBox (v7.x)
* **Guest Machine:** Kali Linux (`kali-linux-2026.3-virtualbox-amd64`)
* **Network Mode:** Dedicated `NATNetwork`
* **Subnet:** `10.0.0.0/24`[cite: 8]
* **Assigned Kali IP:** `10.0.0.2`[cite: 8]
* **Gateway & DNS:** `10.0.0.1`
* **Host Integration:** Bidirectional Clipboard & Drag-and-Drop enabled[cite: 8]
* **Shared Folder:** Host `/downloads` directory mounted[cite: 8]

+-------------------------------------------------------------+
|               Host Operating System (Windows)               |
|               Host Shared Path: /downloads                  |
|                                                             |
|   +-----------------------------------------------------+   |
|   |         VirtualBox Isolated NATNetwork              |   |
|   |         Subnet: 10.0.0.0/24 | DHCP Enabled          |   |
|   |                                                     |   |
|   |         +----------------------------------+        |   |
|   |         | Kali Linux (Attacker VM)         |        |   |
|   |         | Static IP: 10.0.0.2/24           |        |   |
|   |         | Gateway: 10.0.0.1                |        |   |
|   |         | Internet Outbound: Verified      |        |   |
|   |         +----------------------------------+        |   |
|   +-----------------------------------------------------+   |
+-------------------------------------------------------------+


---

## 🛠️ Step-by-Step Lab Execution

### Step 1: Base Virtualization & Kali Linux Initialization
1. Installed and initialized Oracle VM VirtualBox[cite: 8].
2. Imported the `kali-linux-2026.3-virtualbox-amd64` appliance.
3. Configured base system resources (CPU cores, RAM allocation, and video memory).
4. Booted into the desktop environment to verify system integrity.

![Kali Linux Desktop Environment](Kali%20linux.png)

---

### Step 2: Shared Clipboard, Drag-and-Drop & Shared Folders
1. Under the VM menu bar (**Devices -> Shared Clipboard** and **Devices -> Drag and Drop**):
   - Configured **Shared Clipboard** to **Bidirectional** and enabled clipboard file transfers[cite: 8, 23].
   - Configured **Drag and Drop** to **Bidirectional**[cite: 8, 24].

![Shared Clipboard Configuration](clipboard%20screnshot.png)

![Drag and Drop Configuration](drag%20and%20drop%20screenshot.png)

2. Under **Settings -> Shared Folders**:
   - Mapped the host machine's `/downloads` directory with auto-mount enabled for transferring security tools and lab documentation[cite: 8].

---

### Step 3: Dedicated NAT Network Creation
1. Opened VirtualBox **Tools -> Network Manager -> NAT Networks**.
2. Created a new NAT Network named **`NatNetwork`**.
3. Configured the IPv4 prefix to **`10.0.0.0/24`**[cite: 8].
4. Checked **Enable DHCP**.

![VirtualBox NAT Network Configuration](network%20settings%20screenshot.png)

---

### Step 4: Adapter Binding & Manual IP Configuration
1. Attached the Kali Linux network adapter to **`NatNetwork`** under VM Network settings.
2. Inside Kali Linux, configured the **Wired connection 1** settings via NetworkManager GUI:
   - **Method:** `Manual`
   - **Address:** `10.0.0.2`[cite: 8]
   - **Netmask:** `24`[cite: 8]
   - **Gateway:** `10.0.0.1`
   - **DNS Server:** `10.0.0.1`

![Kali Linux IPv4 Manual Configuration](network%20setting%202.png)

---

## ⚠️ Troubleshooting: Resolving Kali Internet & DNS Failure

During the setup on VirtualBox v7 with modern Kali rolling releases, outbound Internet traffic stalled due to IPv4 Duplicate Address Detection (DAD) timeouts within NetworkManager.

### Resolution Steps:
The issue was resolved by executing the following commands in the Kali Linux terminal[cite: 9]:

```bash
# 1. Disable IPv4 DAD timeout on the active wired profile
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0

# 2. Deactivate the interface
sudo nmcli connection down "Wired connection 1"

# 3. Bring the interface back online
sudo nmcli connection up "Wired connection 1"
[cite: 9]

Verification:
Outbound reachability and DNS resolution were verified successfully[cite: 8]:

Bash
ping -c 4 8.8.8.8
ping -c 4 google.com
💾 Disaster Recovery Snapshot
With network parameters verified and file sharing operational, a clean baseline snapshot was taken:

Snapshot Name: My new kali

Purpose: Provides a rapid rollback state in case future exploits, scripts, or misconfigurations corrupt the VM environment.

👨‍🏫 Acknowledgments & Mentorship
Special thanks to:

Sir Waqas Karim (CCIE) for the expert guidance, hands-on labs, and technical support[cite: 3].

Networkwalks IT Solutions & Training for the structured cybersecurity training program[cite: 1].
