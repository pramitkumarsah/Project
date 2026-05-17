# Diagrams — COIT20246 Project

This folder contains all network diagrams for the project.

## Files Required

| File | Description | Status |
|------|-------------|--------|
| `lab-network-diagram.drawio` | Lab setup: OpenWRT VM + Windows host with VirtualBox adapter types | Create in draw.io |
| `lab-network-diagram.png` | Exported PNG of lab diagram | Export from draw.io |
| `production-network-diagram.drawio` | Full production network for KarachiPro Consultants premises | Create in draw.io |
| `production-network-diagram.png` | Exported PNG of production diagram | Export from draw.io |

---

## Lab Network Diagram — What to Include

Elements to draw in draw.io for the **lab setup**:

```
[Internet Cloud]
       |
       | (NAT)
       |
[OpenWRT VM Box]
  - eth0: 10.0.2.15/24 (NAT Adapter) → connects to internet
  - eth1/br-mng: 192.168.56.2/24 (Host-Only Adapter) → connects to Windows host
  - Web Server (uhttpd port 80)
  - SSH (Dropbear port 2222)
  - Firewall (iptables)
       |
       | (Host-Only Network: 192.168.56.0/24)
       |
[Windows 11 Host]
  - IP: 192.168.56.1/24
  - Browser (accesses http://192.168.56.2)
  - SSH client (PowerShell/PuTTY)
  - Wireshark
```

**Include:** a table of IP address allocations (see network.md section 4.1.2)

---

## Production Network Diagram — What to Include

Elements to draw in draw.io for the **production design**:

```
[Internet]
    |
[ISP Router: 75.1.0.1/24]
    |
[OpenWRT Firewall]
  WAN: 75.1.0.2/24
  DMZ: 15.10.1.1/24
  LAN: 15.20.1.1/24
  MGMT: 75.30.1.1/24
    |
    +--------+----------+----------+
    |        |          |          |
  [DMZ]   [Staff LAN] [MGMT VLAN]
  15.10.1.0/24  15.20.1.0/24  75.30.1.0/24
    |         |           |
[Web Server] [8x Staff PCs] [IT Support PC]
15.10.1.10   15.20.1.10-17  75.30.1.10
```

**IP addressing uses:** 15 (from ID 1232**9015**) and 75 (from ID 1232**4875**)

---

## draw.io Tips

- Use draw.io online at https://app.diagrams.net/ or download the desktop app
- Use **network diagram shapes** from the shape library (search for "router", "firewall", "PC", "server")
- Label each line/connection with the interface name and IP
- Use different colours for different network segments (e.g., green for LAN, orange for DMZ, red for WAN)
- Export as PNG: File → Export As → PNG (set to 150-200 DPI for clarity)
- Save the .drawio source file and the .png export — both must be in this folder

---

*Last updated: May 2026*
