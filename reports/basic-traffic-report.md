# HTTP Traffic Analysis Report

## Overview
This report analyzes two captured packets (Frames 846 and 894) from a Wireshark capture. The objective is to determine whether the communication was encrypted or sent in plaintext.

## Packet Details

### Frame 846 (HTTP Request)
- **Source IP:** 10.0.0.245
- **Destination IP:** 44.228.249.3
- **Protocol:** HTTP
- **Port:** TCP 80 (default HTTP port)
- **Details:** The request contains a visible `GET / HTTP/1.1` line along with headers such as:
  - `Host: testphp.vulnweb.com`
  - `User-Agent`
  - `Accept`
- **Observation:** All request headers and method are human-readable, indicating plaintext transmission.

### Frame 894 (HTTP Response)
- **Source IP:** 44.228.249.3
- **Destination IP:** 10.0.0.245
- **Protocol:** HTTP
- **Port:** TCP 80
- **Details:** The response contains a visible status line `HTTP/1.1 200 OK` and HTML webpage content in plaintext.
- **Observation:** The response body and headers are visible without decryption.

## Indicators of Unencrypted Traffic
1. **Protocol is HTTP, not HTTPS** – Wireshark labels the traffic as HTTP. Encrypted traffic would show as TLSv1.2/TLSv1.3.
2. **Uses TCP Port 80** – This is the default port for plaintext HTTP, while HTTPS typically uses port 443.
3. **Plaintext Data** – Both request and response content are fully readable without any decoding.
4. **No TLS Handshake** – The packet sequence shows no TLS negotiation (ClientHello/ServerHello) before HTTP communication.

## Conclusion
The captured packets show a plaintext HTTP exchange between the client and server. Since the data is unencrypted, an attacker with access to the network could read both the request and response contents.
