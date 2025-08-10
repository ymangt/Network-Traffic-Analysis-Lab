# Network Traffic Analysis Report – VM Apache HTTP Capture

## Overview
This report documents HTTP traffic captured on the Test-Machine VM while accessing http://connectivity-check.ubuntu.com over port 80. The objective was to evaluate unencrypted traffic risks in a virtualized environment.

## Observations
- **Packet Count**: Captured 2 packets (request and response).
- **Protocols Detected**: Ethernet II → IPv4 → TCP → HTTP.
- **Request**: HTTP GET `/` to connectivity-check.ubuntu.com (Port 80).
- **Response**: HTTP/1.1 204 No Content from nginx/1.14.0 (Ubuntu), likely due to a connectivity check with no data to return.
- **Vulnerability Identified**: HTTP request and response transmitted in cleartext, allowing inspection of headers and server details.
- **Data Visibility**: Host header, method, and server banner (e.g., "nginx/1.14.0") are exposed.

## Mitigation Recommendations
- **Migrate to HTTPS**: Use TLS certificates (e.g., Let’s Encrypt) to secure traffic.
- **Network Scanning**: Employ Nmap to audit open ports and enforce HTTPS-only policies.
- **Server Hardening**: Update Apache, disable unnecessary headers, and restrict access.

## Lessons Learned
- Mastered VM configuration and Apache setup for traffic simulation.
- Recognized that unencrypted responses (e.g., 204 No Content) still reveal infrastructure details, necessitating encryption.
- Future simulations will include port scans and XSS to broaden threat analysis.

## Conclusion
The VM capture confirms unencrypted HTTP traffic’s vulnerability, even with no content responses, highlighting the need for TLS across all environments. This will guide future secure protocol testing.
