# 🛡️ Penetration Testing Report — Footprinting & Network Scanning

![Cybersecurity](https://img.shields.io/badge/Domain-Cybersecurity%20%26%20Ethical%20Hacking-red?style=for-the-badge&logo=target)
![Kali Linux](https://img.shields.io/badge/OS-Kali%20Linux%202026-blue?style=for-the-badge&logo=kalilinux)
![Nmap](https://img.shields.io/badge/Scanner-Zenmap%20%2F%20Nmap-darkgreen?style=for-the-badge&logo=nmap)
![Internship](https://img.shields.io/badge/Program-Networkwalks-orange?style=for-the-badge)

---

## 📋 Project Information

| Field | Detail |
| :--- | :--- |
| **Pentester Name** | Fortunate Dube |
| **Program / Batch** | Networkwalks Cybersecurity Internship (Batch084 2026) |
| **Project Code** | `W2-PM-FINAL` |
| **Date** | 12 November 2026 |
| **Target Scope** | 1. `networkwalks.com` (Public web target — authorized scope)<br>2. `192.168.56.0/24` (Local authorized LAN subnet) |
| **Client Authorization** | Secured & Documented |
| **Modules Completed** | **W2-PM1:** Footprinting with Multiple Kali Tools<br>**W2-PM5:** Network Scanning with Zenmap |

---

## ⚖️ 1. Liability Disclaimer
> **Notice:** I have performed all activities in this project solely on systems, devices, and domains where I had secured written permission or on devices I own myself. All material herein is for educational and research purposes only. Unauthorized testing, attacking, or scanning of systems outside authorized scope is illegal under international computer misuse legislation.

---

## 🎯 2. Project Overview & Scope
This project represents the practical milestone for **Week 2** of the Networkwalks Cybersecurity Internship. It covers the two fundamental foundational phases of penetration testing:
1. **Phase 1: Reconnaissance & Passive Footprinting (W2-PM1):** Gathering intelligence against `networkwalks.com` using six standard Kali Linux CLI tools without intrusive scanning.
2. **Phase 2: Active Network Scanning (W2-PM5):** Subnet identification, host discovery (ping sweeps), and network topology mapping using **Zenmap** (Nmap GUI).

---

## 🛠️ 3. Tools & Environment

| Tool | Category | Operational Purpose |
| :--- | :--- | :--- |
| **Kali Linux** | Operating System | Primary penetration testing platform |
| **Windows 10/11** | Operating System | Host system used for local network interface analysis |
| **whois** | CLI Recon | Identifies domain registration, registrar, dates, and nameservers |
| **whatweb** | Web Scanner | Fingerprints CMS, web server software, plugins, and frameworks |
| **nslookup** | DNS Query | Resolves domain names to routable IPv4 addresses |
| **curl -I** | HTTP Client | Inspects HTTP response headers, server banners, and REST API links |
| **wafw00f** | WAF Fingerprinting | Identifies Web Application Firewalls protecting the target |
| **dnsrecon** | DNS Enumeration | Maps all DNS records (NS, MX, SPF, TXT, SRV) and BIND daemon version |
| **Zenmap (Nmap GUI)** | Network Scanner | Discovers live hosts, maps subnets, and visualizes network topology |

---

## 🔬 4. Phase 1: Footprinting & Reconnaissance (W2-PM1)

### Task 1: Domain Registration Footprinting (`whois`)
- **Command:**
  ```bash
  whois networkwalks.com
  ```
- **Findings:**
  - **Registrar:** `GoDaddy.com, LLC`
  - **Name Servers:** `NS6135.HOSTGATOR.COM`, `NS6136.HOSTGATOR.COM` (reveals HostGator as primary hosting infrastructure)
- **Security Context:** Attackers utilize nameserver information to map the hosting provider and target support desks or cloud accounts through social engineering.
- **Evidence:**
  ![Task 1 - whois](screenshots/task1_whois.png)
 - **Raw Log File:** [📄 View whois_output.txt](outputs/whois_output.txt)
- 
   

---

### Task 2: Web Technology Fingerprinting (`whatweb`)
- **Command:**
  ```bash
  whatweb networkwalks.com
  ```
- **Findings:**
  - **Web Server:** Apache
  - **CMS:** WordPress
  - **Plugins & Libraries:** `WP Download Manager`, `Bootstrap`, `jQuery`
  - **Information Leaks:** Disclosed public administrator/support email address
- **Security Context:** Disclosed software versions allow an attacker to cross-reference the National Vulnerability Database (NVD) / CVE archives for unpatched vulnerabilities.
- **Evidence:**
  ![Task 2 - whatweb](screenshots/task2_whatweb.png)
- **Raw Log File:** [📄 View whatweb_output.txt](outputs/whatweb_output.txt)


---

### Task 3: DNS Resolution (`nslookup`)
- **Command:**
  ```bash
  nslookup networkwalks.com
  ```
- **Findings:**
  - **Resolved Target IP:** `192.232.216.135`
- **Security Context:** Resolving the domain to an explicit IP address enables direct IP-targeted port scanning and bypasses certain domain-level routing controls.
- **Evidence:**
  ![Task 3 - nslookup](screenshots/task3_nslookup.png)
- **Raw Log File:** [📄 View nslookup_output.txt](outputs/whatweb_output.txt)
- 
---

### Task 4: HTTP Response Header Inspection (`curl -I`)
- **Command:**
  ```bash
  curl -I https://networkwalks.com
  ```
- **Findings:**
  - **Server Banner:** `Server: Apache`
  - **API Exposure:** `Link: <https://networkwalks.com/wp-json/>; rel="https://api.w.org/"`
  - **Caching Architecture:** Endurance / Nginx caching layers present
- **Security Context:** Exposing the WordPress REST API endpoint (`/wp-json/`) allows attackers to enumerate active usernames and system metadata.
- **Evidence:**
  ![Task 4 - curl](screenshots/task4_curl.png)
- **Raw Log File:** [📄 View curl_output.txt](outputs/curl_output.txt)

---

### Task 5: Web Application Firewall Detection (`wafw00f`)
- **Command:**
  ```bash
  wafw00f networkwalks.com
  ```
- **Findings:**
  - **WAF Detected:** `ModSecurity (SpiderLabs) WAF`
- **Security Context:** While ModSecurity protects against baseline automated injection attacks, identifying the exact engine informs an adversary which payload evasion techniques to deploy.
- **Evidence:**
  ![Task 5 - wafw00f](screenshots/task5_wafw00f.png)
- **Raw Log File:** [📄 View wafw00f_output.txt](outputs/wafw00f_output.txt)
---

### Task 6: DNS Record Enumeration (`dnsrecon`)
- **Command:**
  ```bash
  dnsrecon -d networkwalks.com
  ```
- **Findings:**
  - **Mail Server (MX):** `mail.networkwalks.com`
  - **SPF Policy:** `v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`
  - **SRV Records:** Discovered `_autodiscover._tcp` records pointing to cPanel mail infrastructure
  - **BIND Software Version:** `Bind Version 9.16.23-RH`
- **Security Context:** Leaking BIND daemon version information allows attackers to target version-specific vulnerabilities in DNS daemons.
- **Evidence:**
  ![Task 6 - dnsrecon](screenshots/task6_dnsrecon.png)
- **Raw Log File:** [📄 View dnsrecon_output.txt](outputs/dnsrecon_output.txt)
---

## 📡 5. Phase 2: Network Scanning with Zenmap (W2-PM5)

### 5.1 Network Interface & Subnet Calculation
Using the Windows Command Prompt (`ipconfig`), I extracted the network parameters for the local testing environment:
- **Local IP Address:** `192.168.56.1`
- **Subnet Mask:** `255.255.255.0`
- **Calculated LAN Subnet:** `192.168.56.0/24` (Address space: `192.168.56.1` – `192.168.56.254`)

---

### 5.2 Host Discovery & Nmap Output (`Nmap Output` Tab)
- **Scan Profile:** `Ping scan`
- **Command Executed:** `nmap -sn 192.168.56.0/24`
- **Observation:** Scanned 256 IP addresses in 13.71 seconds. Confirmed active host `192.168.56.1` is up and responsive.
- **Evidence:**
  ![Nmap Raw Output](screenshots/zenmap_nmap_output.png)

---

### 5.3 Port & Service Enumeration (`Ports / Hosts` Tab)
- **Scan Profile:** `Quick scan` / `Intense scan`
- **Command Executed:** `nmap -T4 -F 192.168.56.1` (or `nmap -T4 -A -v 10.0.0.2`)
- **Open Ports & Services Discovered:**

| Port | Protocol | State | Service | Reason / Banner |
| :---: | :---: | :---: | :---: | :---: |
| **22** | TCP | `open` | `ssh` | OpenSSH (Secure Remote Administration) |
| **80** | TCP | `open` | `http` | Apache HTTP Server (Web Server Daemon) |

- **Security Impact:** Open ports represent potential ingress points into the network. Listening services must be audited to ensure outdated versions or default credentials are not present.
- **Evidence:**
  ![Zenmap Ports and Hosts](screenshots/zenmap_ports.png)

---

### 5.4 Host Details & System State (`Host Details` Tab)
The **Host Details** panel aggregates system diagnostics collected during scanning:
- **Host Status:** Up
- **Addresses:** IPv4: `192.168.56.1` | MAC: `0A-00-27-00-00-08` (VirtualBox Adapter)
- **Ports Scanned:** Filtered/Open status breakdown
- **OS Detection / Latency:** Recorded round-trip latency (< 0.001s)
- **Evidence:**
  ![Zenmap Host Details](screenshots/zenmap_host_details.png)

---

### 5.5 Interactive Network Topology (`Topology` Tab)
Zenmap's topology engine renders a radial diagram mapping nodes relative to the scanner:
- **Subnet Visualization:** Displays active hosts radiating from `localhost` / scanner origin.
- **Legend & Export:** Enabled the Zenmap Legend window and exported the graphic in PDF format.
- **Exported File:** [`screenshots/topography.pdf`](screenshots/topography.pdf)
- **Evidence:**
  ![Zenmap Topology Map](screenshots/zenmap_topology.png)

---

### 5.6 Scan History & Profile Management (`Scans` Tab)
The **Scans** tab maintains a persistent audit log of all scans run during the session, allowing security analysts to compare baseline scans over time:
- Scans recorded: `Ping scan`, `Quick scan`, `Intense scan`.
- **Evidence:**
  ![Zenmap Scans History](screenshots/zenmap_scans.png)

  

## ⚠️ 6. Risk Analysis Matrix

| # | Observation / Finding | Potential Threat Vector | Severity |
| :-: | :--- | :--- | :-: |
| **1** | Exposed CMS & Plugin Names | Allows adversaries to search for known CVE exploits | 🟡 Medium |
| **2** | BIND DNS Version Disclosure | DNS server exploits targeting version 9.16.23 | 🟡 Medium |
| **3** | Direct Origin IP Identification | Bypasses front-end CDN/caching mitigations | 🟢 Low |
| **4** | WordPress REST API `/wp-json/` Public | Unauthenticated user account enumeration | 🟢 Low |
| **5** | WAF Fingerprinted (ModSecurity) | Adversary customizes evasion techniques for CRS rules | 🟢 Low |

---

## 💡 7. Hardening Recommendations
1. **Banner Masking:** Configure Apache (`ServerTokens Prod`, `ServerSignature Off`) and BIND (`version none;`) to suppress version headers.
2. **REST API Protection:** Restrict unauthenticated access to `/wp-json/wp/v2/users` to prevent username scraping.
3. **Keep Rule Sets Updated:** Regularly patch ModSecurity OWASP Core Rule Set (CRS) to mitigate zero-day payloads.
4. **Periodic Subnet Audits:** Perform recurring Nmap discovery scans to detect unapproved devices on the subnet.

---

## 🔧 8. Troubleshooting Experience
> **Encountered Issue:** During initial Zenmap scanning, the host scan returned zero open ports and an error on multiple IP inputs.  
> **Root Cause:** Kali Linux disables background network daemons by default, and targets were separated by a comma (`,`) rather than a space.  
> **Solution:** Corrected the command syntax to space-separated arguments (`nmap -sn 192.168.56.0/24`) and activated local test services (`sudo systemctl start apache2 ssh`) to verify detection functionality.

---

## 📁 9. Repository Structure
├── README.md                                          # Full interactive report
├── W2-PM-FINAL_Pentesting_Report_Nate_Mthembo.pdf     # Downloadable PDF report
│
├── outputs/                                           # 6 text files
│   ├── whois_output.txt
│   ├── whatweb_output.txt
│   ├── nslookup_output.txt
│   ├── curl_output.txt
│   ├── wafw00f_output.txt
│   └── dnsrecon_output.txt
│
└── screenshots/                                       # screenshots & topology PDF
    ├── task1_whois.png
    ├── task2_whatweb.png
    ├── task3_nslookup.png
    ├── task4_curl.png
    ├── task5_wafw00f.png
    ├── task6_dnsrecon.png
    ├── zenmap_host_discovery.png
    ├── zenmap_ports.png
    ├── zenmap_topology.png
    └── topography.pdf

---
*Authored by **Fortunate Dube** as part of the Networkwalks Cybersecurity Internship.*
