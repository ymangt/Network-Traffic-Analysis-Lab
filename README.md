# Network Traffic Analysis with Wireshark – Lab

A hands-on, portfolio-ready lab that captures and analyzes normal traffic and simulated attacks inside a safe VMware NAT environment. The project demonstrates detection of **unencrypted HTTP**, **reconnaissance (ICMPv6)**, **exploitation (XSS)**, **denial of service (SYN flood)**, and **legacy protocol risk (FTP creds in cleartext)**.

---

## Table of Contents
- [Lab Environment](#lab-environment)
- [Repository Structure](#repository-structure)
- [Quick Start](#quick-start)
- [Scenarios & How to Reproduce](#scenarios--how-to-reproduce)
  - [1) Apache HTTP (Unencrypted)](#1-apache-http-unencrypted)
  - [2) ICMPv6 Port Scan](#2-icmpv6-port-scan)
  - [3) XSS Simulation](#3-xss-simulation)
  - [4) SYN Flood (DDoS) Simulation](#4-syn-flood-ddos-simulation)
  - [5) FTP Credential Capture](#5-ftp-credential-capture)
- [Wireshark Filters Cheat Sheet](#wireshark-filters-cheat-sheet)
- [Threat Impact Summary](#threat-impact-summary)
- [Mitigation Recommendations](#mitigation-recommendations)
- [Screenshots](#screenshots)
- [Reports](#reports)
- [Notes & Safety](#notes--safety)

---

## Lab Environment

- **Host:** Wireshark installed; capturing on **VMware Network Adapter VMnet8**.
- **VM:** Ubuntu (“Test-Machine”) on VMware Workstation Pro  
  - **IP (NAT):** `192.168.71.131`
  - **Services used:** Apache (HTTP/80), `vsftpd` (FTP/21)
- **Tools:** Wireshark, `nmap`, `hping3`, Apache, `vsftpd`

> All traffic is generated in an isolated NAT lab network.

---

## Repository Structure

```text
/screenshots/                    # PNG evidence used in README & reports
  http-get-screenshot.png
  http-response-screenshot.png
  apache-204-screenshot.png
  apache-get-screenshot.png
  port-scan-ICMP-packets-screenshot.png
  XSS-simulated-attack-screenshot.png
  SYN-flood-simulated-attack-screenshot.png
  FTP-credential-capture-screenshot.png
/reports/
  basic-traffic-report.md        # Host PC HTTP baseline
  advanced-traffic-report.md     # VM advanced report (final, merged)
README.md
```

## Quick Start

1. **Start capture** on the host’s **VMnet8** (no capture filter).
2. **Run a scenario** from the sections below.
3. **Apply display filters** (see cheat sheet) to isolate evidence.
4. **Screenshot key packets** and save in `/screenshots`.
5. **Update reports** in `/reports`.

---

## Scenarios & How to Reproduce

### 1) Apache HTTP (Unencrypted)

**What:** HTTP GET over port 80 (unencrypted).\
**Reproduce (VM):**

```bash
curl -I http://connectivity-check.ubuntu.com
```

**Wireshark Display Filter:**

```nginx
http
```

**Evidence:** Host header, HTTP method/status visible in plaintext.

### 2) ICMPv6 Port Scan

**What:** Reconnaissance noise during port scan; MLDv2 multicast reports. Multicast group membership updates observed during scan.\
**Reproduce (VM):**

```bash
sudo nmap -p- localhost
```

**Wireshark Display Filter:**

```nginx
icmpv6
```

**Risk:** Low in lab; link-local info can aid recon in untrusted networks.

### 3) XSS Simulation

**What:** Reflected XSS payload returned by the server in HTTP 200 OK.\
**Setup (VM):** Create `/var/www/html/test.html` with a simple form and script.\
**Trigger (Host or VM):**

```bash
curl "http://192.168.71.131/test.html?input=asd"
```

**Wireshark Display Filters:**

```nginx
http
tcp.port == 80
```

**Payload visible:**

```html
<script>alert('XSS Attack Simulated');</script>
```

### 4) SYN Flood (DDoS) Simulation

**What:** High-rate SYNs causing half-open connections on port 80.\
**Reproduce (VM attacker):**

```bash
sudo apt-get update && sudo apt-get install -y hping3
sudo hping3 -S -p 80 --flood --rand-source 192.168.71.131
# Run 5–10s, then Ctrl+C
```

**Wireshark Display Filters:**

```ini
tcp.flags.syn == 1 and tcp.flags.ack == 0
tcp.port == 80
```

### 5) FTP Credential Capture

**What:** USER/PASS in plaintext on FTP control channel.\
**Server (VM):** `sudo apt install -y vsftpd` and use a minimal config; ensure it listens on `*:21`.\
**User (VM):**

```bash
sudo adduser ftpuser # set pass123 (lab only)
```

**Login (Host or VM):**

```bash
ftp 192.168.71.131
# USER ftpuser
# PASS pass123
```

**Wireshark Display Filters:**

```kotlin
ftp || ftp-data
ftp.request.command == "USER" || ftp.request.command == "PASS"
```

## Wireshark Filters Cheat Sheet

```text
# HTTP
http
tcp.port == 80
# ICMPv6 port-scan noise
icmpv6
# SYN flood
tcp.flags.syn == 1 and tcp.flags.ack == 0
tcp.port == 80
# FTP creds
ftp || ftp-data
ftp.request.command == "USER" || ftp.request.command == "PASS"
```

## Threat Impact Summary

| Threat | Risk | Likelihood | Severity | Potential Impact |
| --- | --- | --- | --- | --- |
| Unencrypted HTTP | High | High | High | Credential theft, MITM, content tampering |
| Port Scan Exposure (ICMPv6) | Low | Medium | Low | Reconnaissance; mapping services |
| Reflected XSS (HTTP) | High | High | High | Session hijacking, data theft |
| SYN Flood (DDoS) | High | High | High | Service outage, denial of access |
| FTP Cleartext Credentials | High | High | High | Account compromise, lateral movement, data leak |

## Mitigation Recommendations

- **Migrate to HTTPS:** Enforce TLS (e.g., Let’s Encrypt) for all web traffic.
- **Close Unused Ports:** UFW/iptables to block unnecessary services (e.g., `sudo ufw deny 631`).
- **IPv6 Hardening:** Disable where unnecessary or restrict ICMPv6 types.
- **Input Validation & Output Encoding:** Prevent XSS via server-side sanitization and proper output encoding.
- **SYN Flood Protections:** Enable SYN cookies; apply rate limiting.
- **Replace FTP:** Use SFTP/FTPS; disable vsftpd if not required.
- **Continuous Testing:** Nmap, OWASP ZAP/Burp in regular assessments.
- **Monitoring:** Zeek/Suricata to detect recon, exploitation, and DoS patterns.

## Screenshots

**Basic HTTP Capture**  
[Basic GET HTTP Capture](../screenshots/http-get-screenshot.png)  
[Basic RESPONSE HTTP Capture](../screenshots/http-response-screenshot.png)  
  
**Apache HTTP Capture**  
[Apache RESPONSE Screenshot](../screenshots/apache-204-screenshot.png)  
[Apache GET Screenshot](../screenshots/apache-get-screenshot.png)  

**ICMPv6 Port Scan**  
[ICMPv6 Port Scan Screenshot](../screenshots/port-scan-ICMP-packets-screenshot.png)  

**XSS Simulation**  
[XSS Simulation Screenshot](../screenshots/XSS-simulated-attack-screenshot.png)  

**SYN Flood Simulation**  
[SYN Flood Screenshot](../screenshots/SYN-flood-simulated-attack-screenshot.png)  

**FTP Credential Capture**  
[FTP Credential Capture Screenshot](../screenshots/FTP-credential-capture-screenshot.png)


**Tip:** In GitHub, images render with `![Alt text](path/to/file.png)` if you want to embed them inline.

## Reports

- **Host Baseline:** `reports/basic-traffic-report.md`\
  GET/200 OK over HTTP; proves unencrypted baseline on the host PC.
- **VM Advanced (Final, merged):** `reports/advanced-traffic-report.md`\
  Apache HTTP (unencrypted), ICMPv6 port scan, XSS, SYN flood, FTP creds.

## Notes & Safety

- All tests were executed on a private NAT lab network.
- Attack simulations (SYN flood, XSS, credential capture) were directed only at lab hosts.
- Please do not run flooding or scanning against networks you don’t own or control.
