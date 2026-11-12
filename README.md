# PENETRATION-TESTING-REPORT / SAMPLE
**FOOTPRINTING & NETWORK SCANNING PHASES**  
**W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS**

| Field | Details |
| :--- | :--- |
| **Pentester Name (Cybersecurity Professional)** | Lukman Ademola |
| **Program / Batch** | B084-Networkwalks |
| **Date** | 11th of November 2026 |
| **Modules Completed** | W2-PM1 (Multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| **Client / Target** | 1. Networkwalks (secured written permission already)<br>2. My own local LAN Network (`10.20.20.116`) |
| **Permission Secured from Client?** | Yes |
| **Phases Covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phase 3–5: In Progress |

---

## 1. Liability Disclaimer & Scope Authorization

All reconnaissance, footprinting, and network scanning activities documented in this report were conducted strictly within an authorized scope. Testing was performed exclusively against targets for which explicit written permission was obtained (`networkwalks.com`) and on my local systems owned directly by the author (`10.20.20.116`).

This documentation is published solely for educational, training, and defensive research purposes as part of the Networkwalks Cybersecurity Program. The techniques, commands, and tools demonstrated herein must never be used to probe, access, or disrupt any network or system without prior written authorization from the system owner.

Neither the author, the instructors, nor Networkwalks assume any liability for the misuse of the information contained in this repository. Unauthorized access to computer systems is illegal and punishable under applicable local and international cybersecurity laws, regardless of whether damage occurs. Every reader is solely responsible for ensuring their actions comply with all relevant laws and ethical guidelines.

---

## 2. Introduction
This report covers footprinting the `networkwalks.com` domain using multiple Kali Linux tools (**W2-PM1**) and scanning my own local network with Zenmap (**W2-PM5**). One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. This is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in Kali Linux (footprinting) and on a Windows PC with Zenmap installed (scanning). Every step below includes the exact command used, the result I observed, a screenshot as evidence, and a short note on why the finding matters from an attacker's point of view.

---

## 3. Tools Used
The table below lists each tool used in this report and its purpose.

| Tool | Purpose |
| :--- | :--- |
| **Kali Linux & Windows** | Operating systems used for reconnaissance activities |
| **WHOIS** | Find domain registration details (owner, dates, name servers). |
| **whatweb** | Fingerprint web technologies (server, CMS, plugins, IP). |
| **nslookup** | Resolve the domain name to its IP address using DNS. |
| **curl -I** | Read the HTTP response headers of the website. |
| **wafw00f** | Detect whether a Web Application Firewall protects the site. |
| **dnsrecon** | Enumerate all DNS records (NS, MX, SPF, TXT, SRV). |
| **Zenmap (Nmap GUI)** | Scan the local subnet to find live hosts, IPs, and MAC addresses. |
| **Windows CMD** | Local IP and MAC address identification |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance
I performed reconnaissance against the `networkwalks.com` domain using six Kali Linux tools: **WHOIS**, **WhatWeb**, **Nslookup**, **Curl**, **Wafw00f**, and **DNSRecon**. Each tool was used to collect and identify a different type of information about the target.

* **WHOIS:** Used to obtain publicly available domain registration information and identify the domain’s name servers (`://hostgator.com` and `://hostgator.com`), confirming registration through **GoDaddy.com, LLC**.
* **WhatWeb:** Used to identify technologies powering the website. The results identified **WordPress 7.0.4** (and **7.1.4** on HTTPS redirect) alongside **WP Download Manager 3.3.58**, **Apache**, **Bootstrap 7.0.4**, and **jQuery 3.7.1**.
* **Nslookup:** Resolved the domain name to its hosting IPv4 address, identified as **`192.232.216.135`**.
* **Curl (`-I`):** Inspected the HTTP/2 response headers, revealing server details (`Apache`), caching rules (`x-nginx-cache: WordPress`), cookie security attributes (`HttpOnly`, `Secure`), and the exposed WordPress REST API endpoint (`/wp-json/`).
* **Wafw00f:** Determined whether a Web Application Firewall was protecting the website, confirming the site is behind **ModSecurity (SpiderLabs)** WAF.
* **DNSRecon:** Enumerated DNS records, providing information relating to name servers, mail servers, SPF/TXT records, service records, and DNS software configuration.

### 4.2 Network Scanning with Zenmap
For the second activity, I used Zenmap to perform network discovery on my local network. The practical required me to identify my local IP address and subnet, discover live hosts, identify their open ports and services, and generate a network topology.

* I first used the Windows `ipconfig` command to identify my local IP address.
* I then entered my host target into Zenmap (`nmap -T4 -F 10.20.20.116`) and ran a **Quick Scan** to identify active services.
* The Zenmap scan identified one live host belonging to my own system:
  * **`10.20.20.116`** (with open ports `135/tcp msrpc`, `139/tcp netbios-ssn`, and `445/tcp microsoft-ds`, while 97 TCP ports were closed).
* After completing the scan, I opened the **Topology** section in Zenmap, enabled the **Legend**, and saved the network topology graphic as required by the practical task.

> **Note:** The IP address `10.20.20.116` belongs to my own authorized local machine.

---

## 5. Risk Analysis / Impact
Based on the information collected during the footprinting and network scanning activities, I identified the following potential risks:

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
| :--- | :--- | :--- | :--- | :---: |
| **1** | **Web technology information exposed** | WhatWeb identified WordPress (`7.0.4` / `7.1.4`) and WP Download Manager (`3.3.58`) | Attackers may use exposed technology/version information to identify software requiring further security review | 🟡 **Medium** |
| **2** | **Server IP address identifiable** | Nslookup resolved the domain to `192.232.216.135` | Provides information about the network location of the web service | 🟢 **Low** |
| **3** | **HTTP technical information exposed** | Curl returned HTTP response headers and exposed `/wp-json/` | May assist technology fingerprinting and further enumeration | 🟢 **Low** |
| **4** | **WAF technology identifiable** | Wafw00f identified ModSecurity (SpiderLabs) | Reveals information about the web application’s security architecture | 🟢 **Low** |
| **5** | **DNS infrastructure information exposed** | DNSRecon identified DNS, mail, and service-related records | DNS information can help build a broader infrastructure profile | 🟡 **Medium** |
| **6** | **Local networking ports visible on host** | Zenmap identified open ports (`135`, `139`, `445`) on `10.20.20.116` | File-sharing and RPC services could be targeted if exposed on untrusted networks | 🟡 **Medium** |

**Risk Level Key:** 🔴 Critical | 🟡 Medium | 🟢 Low

> *Disclaimer:* The risks above are observations from the footprinting and scanning exercises, not confirmed vulnerabilities. These practical exercises involved information gathering and host discovery without active exploitation. Therefore, the presence of a software version, IP address, or DNS record does not by itself mean the system is vulnerable without further authorized validation.

---

## 6. Recommendations
Based on the observations from these activities, I recommend the following security improvements:

1. **Review publicly exposed technology information:** Organizations should regularly review what information about their web technologies, CMS, and plugins is publicly visible.
2. **Keep software updated:** CMS platforms, plugins, and other web technologies should be regularly updated and reviewed against current security advisories.
3. **Review HTTP headers:** HTTP response headers should be reviewed to determine whether unnecessary technical information is being exposed.
4. **Review DNS records regularly:** DNS records should be checked periodically to ensure that only required information and services are publicly exposed.
5. **Properly configure and monitor the WAF:** Keep the WAF (**ModSecurity**) enabled and tuned at all times, since it already filters common automated attacks.
6. **Perform regular internal network discovery:** Organizations should periodically scan their own networks to identify active devices and open services.
7. **Investigate unknown devices:** Any unexpected device discovered during network scanning should be investigated and verified.
8. **Maintain network documentation:** Network topology and device information should be documented and updated regularly.
9. **Perform security testing with authorization:** Reconnaissance and scanning should only be performed against systems and networks where appropriate authorization has been provided and approved.

---

## 7. Conclusion
During my week 2 internship on my cybersecurity and Ethical hacking, These exercises showed me that information gathering is a foundational part of cybersecurity. Before attempting to exploit a system, a security professional can learn a significant amount about an environment by carefully analyzing publicly available data and network responses. I also learned how to document technical findings professionally and why reconnaissance must always remain strictly within an authorized scope
I completed practical activities covering footprinting, reconnaissance, and network scanning.

In the footprinting activity, I worked with six Kali Linux tools to collect information about the target domain. I learned and observed how **WHOIS** provides domain registration data, **WhatWeb** identifies web technologies, **Nslookup** resolves domain names, **Curl** inspects HTTP headers, **Wafw00f** detects Web Application Firewalls, and **DNSRecon** enumerates DNS records.

In the network scanning activity, I used **Zenmap** to inspect my local network configuration, discover active ports like (`135`, `139`, `445`), and generate a visual network topology.


8. Collected Evidences
   Screenshots collected as evidence during the activities (stored in the screenshots/ folder):
A.  WHOIS Domain Lookup (whois networkwalks.com)

<img width="1050" height="551" alt="image" src="https://github.com/user-attachments/assets/38327957-2e2a-43ee-a342-ff7d0649ed79" />

B. WhatWeb Technology Fingerprinting (whatweb networkwalks.com)

<img width="1050" height="552" alt="image" src="https://github.com/user-attachments/assets/ef8eed5d-5189-4343-b94c-dceca50694a6" />

C. Curl HTTP Header Inspection (curl -I https://networkwalks.com)

<img width="1050" height="536" alt="image" src="https://github.com/user-attachments/assets/0e5b39b6-4d5f-4e1d-845a-943e9ab2f6c7" />

D. Nslookup DNS Resolution (nslookup networkwalks.com)

 <img width="1050" height="525" alt="image" src="https://github.com/user-attachments/assets/e24d55a7-f232-4948-9340-0ac12e3e7870" />
  
E. Wafw00f WAF Detection (wafw00f networkwalks.com)

<img width="1050" height="556" alt="image" src="https://github.com/user-attachments/assets/ea883851-263c-4948-beee-f76b11b55184" />

F. Zenmap Quick Scan (nmap -T4 -F 10.20.20.116)

<img width="1050" height="552" alt="image" src="https://github.com/user-attachments/assets/80c530ac-141e-400d-9659-c8f33af24abb" />

G. Zenmap Network Topology & Legend

<img width="1050" height="557" alt="image" src="https://github.com/user-attachments/assets/f21ce491-33a6-4ff5-9535-82519cd62dee" />


## 👤 Author & Project Information

**Lukman Ademola**  
*Cybersecurity Analyst | Penetration Testing & Network Security*

* **LinkedIn:** [:www.linkedin.com/in/lukman-ademola-08127182)
* **Program:** Cybersecurity & Ethical Hacking Internship — Networkwalks
* **Batch / Module:** Batch B084 | Week 02 (`W2-PM-FINAL`)
* **Core Skills Demonstrated:** Passive & Active Reconnaissance, DNS Enumeration










