# 🔐 Cybersecurity Lab Environment Setup

*Building an isolated virtual lab for penetration testing and ethical hacking practice using VMware Workstation and Kali Linux.*

---

## 📌 Project Overview

This project focuses on setting up a virtual cybersecurity and penetration-testing laboratory using VMware Workstation and Kali Linux. The purpose of the lab is to create a controlled environment where cybersecurity tools, network scanning, reconnaissance, vulnerability assessment, and other security-testing activities can be performed safely and repeatedly.

The lab is configured on a private virtual network so that additional target machines can be added later for authorized security testing.

---

## 🎯 Objectives

- Install and configure VMware Workstation hypervisor.
- Install or import Kali Linux as a virtual machine.
- Configure a virtual network using VMware Virtual Network Editor.
- Configure network connectivity and assign a consistent IP address to Kali Linux.
- Verify network connectivity, gateway reachability, and DNS resolution.
- Take a clean baseline VM snapshot for fast recovery.
- Document the complete setup procedure and diagnostic steps.

---

## 🛡️ Purpose of the Lab

The lab provides an isolated and controlled environment for cybersecurity learning and authorized security testing. It supports activities such as:

- Network reconnaissance and port scanning
- Vulnerability assessments
- Packet analysis and traffic sniffing
- Web application security testing
- Exploitation practice and security tool experimentation

> ⚠️ **Important:** This laboratory must only be used for systems that you own or have explicit written permission to test. Do not use this lab environment or its tools to attack unauthorized targets.

---

## 🏗️ Lab Architecture


```

+-------------------------------------------------------+
|                       Host OS                         |
|  +-------------------------------------------------+  |
|  |                VMware Workstation               |  |
|  |  +-------------------+   +-------------------+  |  |
|  |  |   Kali Linux VM   |   |   Target VM       |  |  |
|  |  |   10.0.0.10/24    |   |   (Future Target) |  |  |
|  |  +---------+---------+   +---------+---------+  |  |
|  |            |                       |            |  |
|  |     +------+-----------------------+------+     |  |
|  |     |     VMnet8 (NAT Network)            |     |  |
|  +-----++-----------------------------------+------+  |
+---------+-----------------------------------+---------+

```

---

## ⚙️ Lab Configuration

| 🧩 Component | ⚙️ Configuration |
| :--- | :--- |
| **Host OS** | Windows 10 / 11 |
| **Host RAM** | 8 GB |
| **Processor** | Intel Core i7 / AMD Ryzen |
| **Hypervisor** | VMware Workstation Pro / Player |
| **Security OS** | Kali Linux |
| **Kali RAM** | 2048 MB |
| **Virtual Network** | VMnet8 (NAT) |
| **Network Address** | `10.0.0.0/24` |
| **Kali IP Address** | `10.0.0.10/24` |
| **Default Gateway** | `10.0.0.2` |
| **DNS Server** | `8.8.8.8` |
| **Future Target Range**| `10.0.0.11` – `10.0.0.99` |

---

# 🪜 Lab Setup Procedure

### Step 1. Install Archive Utility (7-Zip)
Download and install [7-Zip](https://7-zip.org/download.html) to extract the downloaded Kali Linux pre-built VMware package (`.7z` or compressed `.ova` archive).

### Step 2. Install VMware Workstation
Install [VMware Workstation](https://www.vmware.com) as the primary hypervisor on the host system.

### Step 3. Configure Virtual Network Editor
1. Open **Edit** > **Virtual Network Editor** in VMware.
2. Select **VMnet8** (NAT) or add a new custom NAT network.
3. Configure the subnet settings:
   - **Subnet IP:** `10.0.0.0`
   - **Subnet Mask:** `255.255.255.0`
4. Click **NAT Settings** and configure the Gateway IP to `10.0.0.2`.
5. Ensure DHCP is enabled for dynamic addressing or reserved static mapping.

### Step 4. Import & Configure Kali Linux
1. Open VMware and select **Open a Virtual Machine** or **Import**.
2. Locate and open the extracted `.vmx` or `.ova` file for Kali Linux.
3. Open **Virtual Machine Settings** > **Network Adapter**.
4. Set the adapter mode to **Custom: Specific virtual network** and select **VMnet8 (NAT)**.
5. Allocate at least **2048 MB RAM** and **2 vCPUs**.

### Step 5. Configure Kali Network Interface
Boot Kali Linux and assign static parameters via CLI or NetworkManager to maintain address persistence:
- **IP Address:** `10.0.0.10`
- **Subnet Mask:** `255.255.255.0` (`/24`)
- **Gateway:** `10.0.0.2`
- **DNS Server:** `8.8.8.8`

### Step 6. Create a Clean VM Baseline Snapshot
1. Power off or pause the VM after verifying initial boot.
2. Navigate to **VM** > **Snapshot** > **Take Snapshot...**.
3. Name the snapshot `Clean Baseline - Network Configured`.
4. This clean recovery point allows immediate restoration if configurations are corrupted during testing.

---

# 🔎 Lab Verification

| ✅ Test | 🧾 Command | 🎯 Expected Result |
| :--- | :--- | :--- |
| **Check IP Address** | `ip a` | Correct IP (`10.0.0.10`) displayed |
| **Test Gateway** | `ping -c 4 10.0.0.2` | 0% packet loss |
| **Test Internet** | `ping -c 4 8.8.8.8` | ICMP replies received successfully |
| **Test DNS Resolution** | `nslookup google.com` | Target hostname resolves to IP |
| **Verify Security Tools** | `nmap --version` | Nmap utility version outputted |
| **Verify Snapshot** | Restore snapshot & run `ip a` | Environment returns to baseline settings |

---

# 🐞 Problems Encountered & Solutions

### Problem 1: No Internet Connectivity After Static IP Setup
- **Cause:** NetworkManager default settings overriding static adapter settings or missing gateway routing rules.
- **Solution:** Execute the connection override command and restart the connection interface:
  ```bash
  sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
  sudo nmcli connection up "Wired connection 1"

```

### Problem 2: Hardware Virtualization Disabled (VT-x / AMD-V Error)

* **Cause:** Intel VT-x or AMD-V extensions turned off in system BIOS/UEFI firmware.
* **Solution:**
1. Restart host PC and access BIOS/UEFI setup (typically F2, F10, or Del).
2. Locate **Intel Virtualization Technology** or **SVM Mode** under CPU options.
3. Enable the feature, save settings, reboot into host OS, and start the VM.



### Problem 3: VMware Network Adapter Stopped Working

* **Cause:** VMware background network services stopped on host.
* **Solution:** Open Windows `services.msc`, locate `VMware DHCP Service` and `VMware NAT Service`, and select **Restart**.

---

# 💡 What I Learned

1. **NAT vs. NAT Network:** A standard NAT isolates guests individually, whereas a **NAT Network** allows multiple target and attacking VMs to intercommunicate on the same virtual subnet while sharing outbound host internet access.
2. **Virtual Machine Networking Modes:** Understanding the difference between Bridged (`VMnet0`), Host-Only (`VMnet1`), and NAT (`VMnet8`) adapters in VMware Workstation.
3. **Static Network Configuration:** Assigning static IPs, gateways, and DNS servers in Linux to maintain predictable diagnostic targets.
4. **Snapshot Management:** Utilizing VMware Snapshot Manager to preserve clean system states before executing complex or hazardous security tools.
5. **Technical Documentation:** Maintaining structured logs of installation steps, command outputs, and troubleshooting procedures for project reproducibility.

---

# 🔐 Security & Ethical Use

This laboratory is intended strictly for educational, research, and authorized security testing purposes. All exercises must be executed within isolated lab networks on systems you own or have explicit authorization to audit.

---

# 🔗 Tools & Resources

* [7-Zip Archiver](https://7-zip.org/download.html)
* [VMware Workstation](https://www.vmware.com)
* [Kali Linux Downloads](https://kali.org/get-kali)

---

# 👤 Author

**Jerom Mbawa** — Cybersecurity Professional

Connect with me: [LinkedIn](https://www.linkedin.com/in/jerom-mbawa-2466242a8) | [GitHub](https://github.com)

---

### 📌 Project Information

* **Program:** Cybersecurity Lab Setup
* **Environment:** VMware Workstation / Kali Linux
* **Repository:** GitHub

```

```
