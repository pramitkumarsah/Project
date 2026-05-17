# Network Setup, Firewall Rules & Network Diagrams

**Task 4.1 — COIT20246 Project**
**Group Members:** Pramit Kumar Sah (12329015) | Hamed Hussain Awad Mohammed (12324875)

---

## 4.1.1 Assumptions

The following assumptions have been made to define our business scenario. All project work is based on these assumptions.

| # | Assumption | Details |
|---|-----------|---------|
| 1 | **Location** | Karachi, Sindh, Pakistan — a major commercial hub with a strong professional services sector |
| 2 | **Business Type** | Legal and Accounting consultancy firm — "KarachiPro Consultants" — providing tax advisory, corporate compliance, and legal documentation services to local businesses and individuals |
| 3 | **Staff** | 8 staff total: 1 Managing Partner, 2 Senior Lawyers, 2 Accountants, 1 IT Support Officer, 1 Receptionist, 1 Office Manager |
| 4 | **Website Content** | A static informational website listing: company name, services offered (legal advisory, tax filing, corporate registrations), contact information (phone, email, office address), office hours, and staff profiles (names and titles only — no personal data) |

**Justification of assumptions:**
- Karachi was chosen as it is the financial capital of Pakistan, making professional consultancy services realistic and common.
- A legal/accounting firm was chosen because such firms handle highly sensitive client data (financial records, legal documents, personal identity information), making the cybersecurity scenario realistic and meaningful.
- 8 staff suits the "5 to 10 staff" requirement in the scenario.
- The website contains only public-facing business information — no client data is published online.

---

## 4.1.2 Network Setup

### Existing Lab Network — Interface Documentation

The following interfaces were identified on the OpenWRT VM using `ip addr` and VirtualBox network adapter settings.

**Command used:** `ip addr show`

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
    link/ether 08:00:27:xx:xx:xx brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global eth0

3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
    link/ether 08:00:27:yy:yy:yy brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.2/24 brd 192.168.56.255 scope global eth1

4: br-mng: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    link/ether 08:00:27:yy:yy:yy brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.2/24 brd 192.168.56.255 scope global br-mng
```

> **📸 Screenshot required here:** Output of `ip addr` command on OpenWRT terminal  
> *File: `screenshots/task4-1-2-ip-addr.png`*

### Interface Summary Table

| Interface | IP Address | Subnet Mask | VirtualBox Adapter | Role |
|-----------|-----------|-------------|-------------------|------|
| eth0 | 10.0.2.15 | /24 | NAT | WAN — connects OpenWRT to the internet through the host machine |
| eth1 | 192.168.56.2 | /24 | Host-Only | LAN — connects OpenWRT to the Windows host |
| br-mng | 192.168.56.2 | /24 | Host-Only | Management bridge — used for OpenWRT LuCI web interface and SSH access |
| lo | 127.0.0.1 | /8 | N/A | Loopback interface (local only) |

### How the Windows Host Connects to OpenWRT

The Windows host connects to OpenWRT through the **Host-Only adapter** (`eth1` / `br-mng`) in VirtualBox. VirtualBox creates a virtual network (192.168.56.0/24) that exists only between the host machine and VMs using the Host-Only adapter. The Windows host is assigned an IP in this range (typically 192.168.56.1 assigned by the VirtualBox DHCP server), and OpenWRT is assigned 192.168.56.2. This allows the Windows host to access the OpenWRT management interface (LuCI on port 81), SSH (port 22), and the web server (port 80) hosted on OpenWRT.

---

### Lab Network Diagram

> **📸 Diagram required here:** draw.io lab network diagram  
> *File: `diagrams/lab-network-diagram.png`*

The diagram below illustrates the lab setup:

```
Internet
    |
    | (NAT - VirtualBox)
    |
+---+-------------------+
|   OpenWRT VM          |
|   eth0: 10.0.2.15/24  |  ← NAT Adapter (WAN)
|   eth1/br-mng:        |
|   192.168.56.2/24     |  ← Host-Only Adapter (LAN/Management)
+---+-------------------+
    |
    | (Host-Only Network: 192.168.56.0/24)
    |
+---+-------------------+
|   Windows 11 Host     |
|   192.168.56.1/24     |
|   (VirtualBox DHCP)   |
+---+-------------------+
```

**Note:** The actual draw.io diagram is in `diagrams/lab-network-diagram.drawio` with the exported PNG linked above.

#### IP Address Allocation Table — Lab Setup

| Device | Interface | IP Address | Purpose |
|--------|-----------|-----------|---------|
| OpenWRT VM | eth0 (NAT) | 10.0.2.15/24 | WAN (internet access via NAT) |
| OpenWRT VM | eth1 / br-mng (Host-Only) | 192.168.56.2/24 | LAN / management access |
| Windows Host | VirtualBox Host-Only | 192.168.56.1/24 | Workstation / admin access |
| Gateway (VirtualBox NAT) | — | 10.0.2.1 | Default gateway for OpenWRT WAN |

---

### Web Server Setup on OpenWRT

A lightweight web server (uhttpd, which is the default on OpenWRT) was configured to host a simple business website.

**Steps performed:**

**Step 1 — Verify uhttpd is installed and running:**
```bash
opkg update
opkg install uhttpd
/etc/init.d/uhttpd start
/etc/init.d/uhttpd enable
```

**Step 2 — Create the business website HTML:**
```bash
cat > /www/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>KarachiPro Consultants</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; background: #f5f5f5; }
        h1 { color: #003366; }
        .info { background: white; padding: 20px; border-radius: 8px; }
    </style>
</head>
<body>
    <div class="info">
        <h1>KarachiPro Consultants</h1>
        <p><strong>Professional Legal &amp; Accounting Services — Karachi, Pakistan</strong></p>
        <hr>
        <h2>Services</h2>
        <ul>
            <li>Tax Advisory &amp; Filing</li>
            <li>Corporate Compliance</li>
            <li>Legal Documentation</li>
            <li>Business Registration</li>
        </ul>
        <h2>Contact</h2>
        <p>Phone: +92-21-XXXX-XXXX | Email: info@karachipro.pk</p>
        <p>Address: Office 5, Commercial Plaza, Clifton, Karachi</p>
        <hr>
        <p><em>Project by: Pramit Kumar Sah (12329015) &amp; Hamed Hussain Awad Mohammed (12324875)</em></p>
        <p><em>Date: May 2026</em></p>
    </div>
</body>
</html>
EOF
```

**Step 3 — Verify uhttpd is listening on port 80:**
```bash
netstat -tlnp | grep :80
```

**Step 4 — Test from Windows host browser:**

Open browser on Windows host and navigate to: `http://192.168.56.2/`

> **📸 Screenshot required here:** Browser on Windows host showing KarachiPro Consultants website  
> *File: `screenshots/task4-1-2-website-browser.png`*

> **📸 Screenshot required here:** `netstat -tlnp` output showing uhttpd listening on port 80  
> *File: `screenshots/task4-1-2-netstat.png`*

**Connectivity Test (ping from Windows to OpenWRT):**
```
ping 192.168.56.2
```

> **📸 Screenshot required here:** Successful ping from Windows host to OpenWRT  
> *File: `screenshots/task4-1-2-ping-test.png`*

---

## 4.1.3 Firewall Rules

All firewall rules are configured using OpenWRT's `iptables` commands via the terminal (SSH). For each rule, we demonstrate the **before state**, **apply the rule**, and confirm the **after state**.

---

### Rule 1 — Block and Allow HTTP (Port 80)

**Purpose:** Controlling HTTP access to the web server protects against unauthorised access to the hosted website and reduces the attack surface when the web server is not needed.

#### Step 1a — Confirm HTTP is accessible (Before state)

From Windows host browser, navigate to `http://192.168.56.2/` — the KarachiPro website loads successfully.

> **📸 Screenshot required:** Website loading before the block rule  
> *File: `screenshots/task4-1-3-http-before-block.png`*

#### Step 1b — Apply block rule for HTTP

```bash
# SSH into OpenWRT
ssh root@192.168.56.2

# Block port 80 (HTTP) on the management/LAN interface
iptables -I INPUT -i br-mng -p tcp --dport 80 -j DROP

# Verify the rule is in place
iptables -L INPUT -n --line-numbers
```

> **📸 Screenshot required:** iptables command and rule list showing DROP rule for port 80  
> *File: `screenshots/task4-1-3-http-block-rule.png`*

#### Step 1c — Confirm website is inaccessible (After block)

From Windows host browser, navigate to `http://192.168.56.2/` — the page should time out or show "Connection refused".

> **📸 Screenshot required:** Browser showing site inaccessible after block  
> *File: `screenshots/task4-1-3-http-after-block.png`*

#### Step 1d — Remove block and allow HTTP

```bash
# Remove the DROP rule (rule number from previous list, e.g., rule 1)
iptables -D INPUT -i br-mng -p tcp --dport 80 -j DROP

# Or explicitly allow HTTP
iptables -I INPUT -i br-mng -p tcp --dport 80 -j ACCEPT

iptables -L INPUT -n --line-numbers
```

> **📸 Screenshot required:** iptables list showing ACCEPT rule, website loading again  
> *File: `screenshots/task4-1-3-http-allow.png`*

**Security Explanation:** Blocking HTTP when not in use is a principle of least privilege — only services actively needed should be reachable. If the web server is ever taken offline for maintenance, blocking port 80 prevents any residual process from being exploited.

---

### Rule 2 — Allow SSH and Change to Non-Standard Port

**Purpose:** SSH access is required for remote management. Using a non-standard port reduces automated brute-force attacks, which typically scan port 22.

#### Step 2a — Confirm SSH on port 22 (Before state)

```bash
# From Windows host (using PowerShell or PuTTY)
ssh root@192.168.56.2 -p 22
```

> **📸 Screenshot required:** Successful SSH connection on port 22  
> *File: `screenshots/task4-1-3-ssh-port22-success.png`*

#### Step 2b — Change SSH to port 2222

```bash
# On OpenWRT — edit dropbear config
uci set dropbear.@dropbear[0].Port=2222
uci commit dropbear
/etc/init.d/dropbear restart
```

Also add firewall rule to allow port 2222:
```bash
iptables -I INPUT -i br-mng -p tcp --dport 2222 -j ACCEPT
```

> **📸 Screenshot required:** uci set commands and dropbear restart  
> *File: `screenshots/task4-1-3-ssh-port-change.png`*

#### Step 2c — Confirm port 22 no longer works, port 2222 works

```bash
# From Windows (should fail)
ssh root@192.168.56.2 -p 22

# From Windows (should succeed)
ssh root@192.168.56.2 -p 2222
```

> **📸 Screenshot required:** Port 22 connection refused; port 2222 successful  
> *File: `screenshots/task4-1-3-ssh-port2222-success.png`*

**Security Explanation:** Changing SSH to a non-standard port is a form of security through obscurity. While not a primary defence, it significantly reduces automated scanning and brute-force attempts, as most bots only target port 22.

---

### Rule 3 — Block and Allow ICMP (Ping)

**Purpose:** Blocking ICMP prevents external hosts from discovering that the router is alive ("ping sweeping"), reducing reconnaissance opportunities for attackers.

#### Step 3a — Confirm ping works (Before state)

```cmd
# From Windows host
ping 192.168.56.2
```

> **📸 Screenshot required:** Successful ping replies from OpenWRT  
> *File: `screenshots/task4-1-3-icmp-before-block.png`*

#### Step 3b — Block ICMP

```bash
# On OpenWRT
iptables -I INPUT -i br-mng -p icmp -j DROP
iptables -L INPUT -n --line-numbers
```

> **📸 Screenshot required:** iptables ICMP DROP rule applied  
> *File: `screenshots/task4-1-3-icmp-block-rule.png`*

#### Step 3c — Confirm ping fails

```cmd
ping 192.168.56.2
```

> **📸 Screenshot required:** Windows ping showing "Request timed out"  
> *File: `screenshots/task4-1-3-icmp-after-block.png`*

#### Step 3d — Re-enable ICMP

```bash
iptables -D INPUT -i br-mng -p icmp -j DROP
# Test ping again — should succeed
```

> **📸 Screenshot required:** Ping working again after removing the block  
> *File: `screenshots/task4-1-3-icmp-re-enabled.png`*

**Security Explanation:** ICMP blocking prevents network reconnaissance. Attackers commonly use ping sweeps to identify live hosts before launching targeted attacks. Blocking ICMP on external-facing interfaces while allowing it on trusted internal networks is a balanced approach.

---

### Rule 4 — Restrict Management Interface Access (Port 81)

**Purpose:** The OpenWRT LuCI web management interface must only be accessible from trusted administrator machines, not from general staff workstations.

#### Step 4a — Confirm LuCI is accessible (Before state)

From Windows host browser: `http://192.168.56.2:81` — LuCI login page loads.

> **📸 Screenshot required:** LuCI login page accessible before restriction  
> *File: `screenshots/task4-1-3-luci-before.png`*

#### Step 4b — Restrict access to management interface

Allow only the Windows host (192.168.56.1) to access port 81, and block all others:

```bash
# Allow specific admin IP
iptables -I INPUT -i br-mng -s 192.168.56.1 -p tcp --dport 81 -j ACCEPT

# Block all other access to port 81
iptables -A INPUT -i br-mng -p tcp --dport 81 -j DROP

iptables -L INPUT -n --line-numbers
```

> **📸 Screenshot required:** iptables rules for management interface  
> *File: `screenshots/task4-1-3-luci-rule.png`*

#### Step 4c — Verify restriction

- From 192.168.56.1 (admin host): LuCI should still load
- A simulated second host with a different IP would be blocked

> **📸 Screenshot required:** LuCI accessible from admin IP; blocked from other IP  
> *File: `screenshots/task4-1-3-luci-restricted.png`*

**Security Explanation:** Restricting management interface access to specific administrator IPs is a critical control. If an attacker gains access to the internal network, they would not be able to access the router's management interface. This limits lateral movement and privilege escalation opportunities.

---

## 4.1.4 Network Diagram — Production Design

### Production Network Design for KarachiPro Consultants

This diagram shows the **full production network** as it would be deployed at the actual business premises in Karachi. This is distinct from the lab setup — the lab simulates the router/firewall and one workstation, while the production design shows the complete office infrastructure.

> **📸 Diagram required here:** draw.io production network diagram  
> *File: `diagrams/production-network-diagram.png`*

The production network includes:
- **Internet connection** via an ISP router (fibre)
- **OpenWRT router/firewall** — perimeter security device
- **DMZ segment** — hosts the public-facing business website
- **Staff LAN segment** — 8 workstations for all staff
- **Management VLAN** — restricted access for IT support only

---

### IP Address Allocation — Production Network

**IP Addressing Rule:** Based on student IDs **12329015** (last two digits: **15**) and **12324875** (last two digits: **75**).
- All IP addresses start with **15** or **75**
- Only /16 or /24 subnet masks are used
- No private 192.168.x.y addresses are used

| Segment | Network Address | Subnet Mask | First Host | Last Host | Broadcast |
|---------|----------------|-------------|-----------|----------|-----------|
| WAN (ISP link) | 75.1.0.0 | /24 | 75.1.0.1 | 75.1.0.254 | 75.1.0.255 |
| DMZ (web server) | 15.10.1.0 | /24 | 15.10.1.1 | 15.10.1.254 | 15.10.1.255 |
| Staff LAN | 15.20.1.0 | /24 | 15.20.1.1 | 15.20.1.254 | 15.20.1.255 |
| Management VLAN | 75.30.1.0 | /24 | 75.30.1.1 | 75.30.1.254 | 75.30.1.255 |

#### Specific Device Allocations

| Device | IP Address | Subnet | Role |
|--------|-----------|--------|------|
| ISP Router (WAN gateway) | 75.1.0.1 | /24 | Default gateway to internet |
| OpenWRT WAN interface | 75.1.0.2 | /24 | Router external IP |
| OpenWRT DMZ interface | 15.10.1.1 | /24 | DMZ gateway |
| OpenWRT LAN interface | 15.20.1.1 | /24 | Staff LAN gateway |
| OpenWRT Management | 75.30.1.1 | /24 | Management interface |
| Web Server (DMZ) | 15.10.1.10 | /24 | Business website host |
| Workstation 1 (Managing Partner) | 15.20.1.10 | /24 | Staff PC |
| Workstations 2–8 (Staff) | 15.20.1.11–17 | /24 | Staff PCs |
| IT Support PC | 75.30.1.10 | /24 | Admin workstation (management VLAN) |

**Note:** The student IDs used for the first octet are: **15** (from 1232**9015**) and **75** (from 1232**4875**). No 192.168.x.y addresses are used in the production design.

---

*Document version: 1.0 | Last updated: May 2026*
