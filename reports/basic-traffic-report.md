# Network Traffic Analysis Report – Host PC HTTP Capture

## Overview
This report documents an HTTP packet capture conducted on the host PC while visiting http://testphp.vulnweb.com over port 80. The objective was to assess the encryption status and identify security risks in unencrypted web traffic.

## Observations
- **Packet Count**: Captured 2 packets (Frames 846 and 894).
- **Protocols Detected**: Ethernet II → IPv4 → TCP → HTTP.
- **Source/Destination Details**:
  - **Frame 846 (GET Request)**: Src 10.0.0.245 → Dst 44.228.249.3, "GET / HTTP/1.1".
  - **Frame 894 (200 OK Response)**: Src 44.228.249.3 → Dst 10.0.0.245, "HTTP/1.1 200 OK".
- **Vulnerability Identified**: HTTP traffic transmitted in cleartext, susceptible to interception, eavesdropping, and manipulation.
- **Data Visibility**: Headers (e.g., "Host: testphp.vulnweb.com") and potential form data are readable without decryption.

## Mitigation Recommendations
- **Use HTTPS**: Implement TLS certificates to encrypt traffic.
- **Network Scanning**: Utilize Nmap to identify and close open ports (e.g., `nmap -p- <IP>`).
- **Intrusion Detection**: Deploy Snort or Suricata to monitor unencrypted patterns.

## Lessons Learned
- Developed skills in packet analysis using Wireshark filters to identify unencrypted traffic.
- Learned that even basic HTTP browsing exposes data, requiring secure protocols.
- Future captures will compare encrypted (HTTPS) traffic to assess mitigation effectiveness.

## Conclusion
The capture confirms that HTTP traffic on port 80 is unencrypted, posing significant risks of data exposure. This underscores the need for encryption in network communications, with further analysis planned using a VM environment.
