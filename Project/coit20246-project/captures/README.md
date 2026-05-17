# Packet Captures — COIT20246 Project

This folder contains the `.pcap` files captured using `tcpdump` on OpenWRT.

## Required Files

| File | Description | How to Capture |
|------|-------------|---------------|
| `http_capture.pcap` | HTTP traffic while browsing the business website | `tcpdump -i br-mng -w /tmp/http_capture.pcap port 80` |
| `ssh_capture.pcap` | SSH session traffic on port 2222 | `tcpdump -i br-mng -w /tmp/ssh_capture.pcap port 2222` |

## How to Transfer pcap Files from OpenWRT to Windows

```powershell
# From Windows PowerShell — transfer HTTP capture
scp -i ~/.ssh/id_ed25519 -P 2222 root@192.168.56.2:/tmp/http_capture.pcap .\captures\http_capture.pcap

# Transfer SSH capture
scp -i ~/.ssh/id_ed25519 -P 2222 root@192.168.56.2:/tmp/ssh_capture.pcap .\captures\ssh_capture.pcap
```

## Opening in Wireshark

1. Install Wireshark on Windows (https://www.wireshark.org/download.html)
2. File → Open → select the .pcap file
3. Apply filters:
   - HTTP: `http`
   - SSH: `tcp.port == 2222`

---

*Last updated: May 2026*
