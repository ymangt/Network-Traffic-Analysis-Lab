# Network Traffic Analysis Report – VM Apache HTTP Capture

## Overview
This report documents HTTP traffic captured on a virtual machine when accessing `http://connectivity-check.ubuntu.com` over **port 80**. The capture demonstrates the risks of unencrypted traffic transmission.

## Observations
- **Packet Count:** Captured a request (Frame 6) and corresponding response (Frame 8).
- **Protocols Detected:** Ethernet II → IPv4 → TCP → HTTP.
- **Request:** HTTP GET `/` to `connectivity-check.ubuntu.com` (Port 80).
- **Response:** HTTP/1.1 204 No Content from an Apache server running on Ubuntu.
- **Vulnerability Identified:** HTTP request and response transmitted in cleartext, allowing full inspection of headers and server response.
- **Data Visibility:** Host header, HTTP method, and server banner are visible in plaintext.

## Mitigation Recommendations
- **Migrate to HTTPS:** Implement TLS certificates (e.g., Let’s Encrypt) to encrypt communication.
- **Port and Service Review:** Perform periodic scanning with `Nmap` to detect services running on insecure ports.
- **Web Server Hardening:** Disable unnecessary headers, update Apache regularly, and enforce HTTPS-only connections.

## Lessons Learned
- VM captures confirm that unencrypted HTTP traffic is equally vulnerable as on a physical host.
- Even non-sensitive HTTP requests reveal server information and infrastructure details.
- Securing server communication with TLS is critical, regardless of the deployment environment.
