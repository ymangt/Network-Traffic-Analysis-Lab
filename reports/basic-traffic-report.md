# Network Traffic Analysis Report – Host PC HTTP Capture

## Overview
This report documents an HTTP packet capture conducted on the **host PC** while visiting `http://testphp.vulnweb.com` over TCP port 80. The objective was to evaluate the encryption status and identify security risks in unencrypted web traffic.

## Observations
- **Packet Count:** 2 packets captured (Frames 846 and 894).
- **Protocols Detected:** Ethernet II → IPv4 → TCP → HTTP.
- **Source/Destination Details:**
  - **Frame 846 (GET Request):** Src 10.0.0.245 → Dst 44.228.249.3, `GET / HTTP/1.1`.
  - **Frame 894 (200 OK Response):** Src 44.228.249.3 → Dst 10.0.0.245, `HTTP/1.1 200 OK`.
- **Vulnerability Identified:** HTTP traffic was transmitted in cleartext, making it susceptible to interception, eavesdropping, and manipulation.
- **Data Visibility:** Request and response headers (e.g., `Host: testphp.vulnweb.com`) and potential form data were fully readable without decryption.
- [Basic GET HTTP Capture](../screenshots/http-get-screenshot.png)
- [Basic RESPONSE HTTP Capture](../screenshots/http-response-screenshot.png)  

## Security Risks
- **Unencrypted Transmission:** All HTTP data could be intercepted by any party with network access.
- **Potential Exploitation:** Data tampering, credential theft, and session hijacking are possible if the traffic is intercepted.

## Mitigation Recommendations
- **Migrate to HTTPS:** Implement TLS certificates to encrypt communication.
- **Network Scanning:** Use tools such as `nmap -p- <IP>` to identify and close unused ports.
- **Intrusion Detection:** Deploy IDS tools (e.g., Snort, Suricata) to flag unencrypted traffic patterns.

## Lessons Learned
- Gained proficiency in using Wireshark filters to isolate and analyze unencrypted HTTP traffic.
- Recognized that even basic web browsing over port 80 exposes sensitive metadata and potential user inputs.
- Established a baseline for comparing encrypted (HTTPS) vs. unencrypted (HTTP) communications in future VM-based captures.

## Conclusion
The capture confirms that HTTP traffic over port 80 is unencrypted, posing significant risks of data exposure. This baseline analysis on the host PC will serve as a reference point for subsequent VM-based captures and advanced threat simulations.


