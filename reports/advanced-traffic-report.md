# Apache HTTP Traffic Capture Report (VM)

## Capture Environment
- **System**: Virtual Machine (VM)
- **Purpose**: Capture of HTTP traffic when accessing a site running Apache or responding with HTTP traffic
- **Network Interface**: VMware virtual adapter
- **Capture Tool**: Wireshark

## Observations

### Frame: HTTP GET Request
- **Source IP**: 192.168.71.131 (VM)
- **Destination IP**: 91.189.91.49 (connectivity-check.ubuntu.com)
- **Source Port**: 43872
- **Destination Port**: 80 (HTTP)
- **HTTP Method**: GET
- **HTTP Version**: 1.1
- **Host Header**: connectivity-check.ubuntu.com
- **Additional Headers**:
  - Accept: */*
  - Connection: close

### Frame: HTTP 204 Response
- **Source IP**: 91.189.91.49
- **Destination IP**: 192.168.71.131
- **Status Code**: 204 No Content
- **Server**: nginx/1.14.0 (Ubuntu)
- **Date**: Sat, 09 Aug 2025 19:08:47 GMT
- **Headers**:
  - x-cache-status: from content-cache-lss/lr
  - x-networkmanager-status: online

## Key Points
- The traffic was **unencrypted HTTP**, meaning all headers and content could be read in plain text.
- The captured traffic demonstrates a full request/response cycle:
  1. The VM sent an HTTP GET request to `connectivity-check.ubuntu.com`.
  2. The server responded with `HTTP/1.1 204 No Content`.
- This confirms HTTP traffic can be easily inspected and intercepted when not using HTTPS.

## Conclusion
This capture from the VM environment successfully shows unencrypted HTTP communication between the client and the server. It will serve as a comparison against future HTTPS or secure protocol captures.
