# Network Traffic Analysis Report – Host PC HTTP Capture

## Overview
This report documents an HTTP packet capture conducted on the host PC while visiting a website over **port 80** (unencrypted HTTP). The purpose was to examine the visibility of unencrypted web traffic.

## Observations
- **Packet Count:** Frame 846 and Frame 894 captured.
- **Protocols Detected:** Ethernet II → IPv4 → TCP → HTTP.
- **Source/Destination Details:**
  - **Frame 846:** Src 10.0.0.245 → Dst 44.228.249.3.
  - **Frame 894:** Src 44.228.249.3 → Dst 10.0.0.245.
- **Vulnerability Identified:** HTTP traffic is transmitted in cleartext, making it susceptible to interception, eavesdropping, and manipulation.
- **Data Visibility:** HTTP headers and potentially sensitive data could be inspected without encryption.

## Mitigation Recommendations
- **Use HTTPS:** Encrypt web traffic with TLS to prevent interception.
- **Network Scanning:** Use tools like `Nmap` to identify open ports and services, ensuring HTTP is replaced with HTTPS where possible.
- **Network Monitoring:** Deploy IDS/IPS systems such as Snort or Suricata to detect unencrypted traffic patterns.

## Lessons Learned
- Unencrypted HTTP exposes communication contents to anyone with network access.
- Even basic browsing over port 80 poses security risks.
- Future captures should prioritize testing secure protocols to compare encryption effectiveness.
