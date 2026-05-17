# Security Hardening, Traffic Analysis & Risk Assessment

**Tasks 4.2 & 4.3 — COIT20246 Project**
**Group Members:** Pramit Kumar Sah (12329015) | Hamed Hussain Awad Mohammed (12324875)

---

## 4.2 Security Hardening and Traffic Analysis

## 4.2.1 Harden the OpenWRT System

Security hardening reduces the attack surface of the OpenWRT router by removing unnecessary access points, strengthening authentication, and disabling unused services. Each step below documents the before state, the change made, and the after state.

---

### Hardening Step 1 — Change Default Root Password

**Security Risk Addressed:** OpenWRT ships with a default or blank root password. If not changed, any user who gains access to the management network can log into the router without credentials — giving them full administrative control over the firewall and all network traffic.

#### Before State

OpenWRT is freshly configured with no root password (or default password). SSH login succeeds without a password prompt, or using a well-known default.

> **📸 Screenshot required:** Login attempt before password change (e.g., password-less SSH or known default)  
> *File: `screenshots/task4-2-1-passwd-before.png`*

#### Change Made

```bash
# On OpenWRT console/SSH session
passwd root
# Enter new password: [strong password — not shown for security]
# Re-enter password to confirm
```

> **📸 Screenshot required:** `passwd` command running and confirming password change  
> *File: `screenshots/task4-2-1-passwd-change.png`*

#### After State

SSH login now requires the new password. Attempting to log in without it fails with "Authentication failed".

> **📸 Screenshot required:** SSH login requiring password; failed attempt with wrong password  
> *File: `screenshots/task4-2-1-passwd-after.png`*

---

### Hardening Step 2 — Examine Password Storage

**Security Risk Addressed:** Storing passwords in plaintext means any attacker who gains read access to the password file immediately has all credentials. Hashing ensures even a compromised file does not directly reveal passwords.

#### Viewing /etc/shadow

```bash
cat /etc/shadow
```

Expected output (example):
```
root:$6$randomsalt$hashedpasswordstring...:19000:0:99999:7:::
```

> **📸 Screenshot required:** Output of `cat /etc/shadow` showing the hash (blur/crop the actual hash if desired)  
> *File: `screenshots/task4-2-1-shadow-file.png`*

#### Identifying the Hashing Algorithm

The hash begins with `$6$` — this identifies the algorithm as **SHA-512**.

| Prefix | Algorithm |
|--------|-----------|
| `$1$` | MD5 (weak — deprecated) |
| `$5$` | SHA-256 |
| `$6$` | SHA-512 (current standard — used by OpenWRT) |
| `$y$` | yescrypt (modern, memory-hard) |

OpenWRT uses **SHA-512** (`$6$`) for password hashing, which is a strong one-way cryptographic function.

#### Why Passwords Are Stored as Hashes

Passwords are stored as **hashes** rather than plaintext for the following reasons:

1. **One-way function:** A cryptographic hash cannot (practically) be reversed to reveal the original password. Even if an attacker reads the shadow file, they cannot directly recover the password.
2. **Salt prevents rainbow tables:** The salt (random string embedded in the hash) ensures that two users with the same password will have different hashes, preventing pre-computed "rainbow table" attacks.
3. **Breach containment:** If the system is compromised and the shadow file is stolen, the attacker must spend significant computational effort (brute force or dictionary attacks) to crack each hash — giving the organisation time to respond and change credentials.
4. **Compliance:** Password hashing is a standard security requirement in frameworks such as NIST SP800-53 (IA-5: Authenticator Management).

---

### Hardening Step 3 — SSH Key-Based Authentication

**Security Risk Addressed:** Password-based SSH authentication is vulnerable to brute-force attacks, credential stuffing, and phishing. Key-based authentication eliminates these risks by requiring possession of a private key file.

#### Before State

SSH login to OpenWRT requires only a password (username + password).

> **📸 Screenshot required:** SSH login with password (before key setup)  
> *File: `screenshots/task4-2-1-ssh-password-login.png`*

#### Generate SSH Key Pair (on Windows host)

```powershell
# In PowerShell on Windows host
ssh-keygen -t ed25519 -C "coit20246-project"
# Save to: C:\Users\<user>\.ssh\id_ed25519
# No passphrase (or set one for extra security)
```

> **📸 Screenshot required:** `ssh-keygen` output showing key generation  
> *File: `screenshots/task4-2-1-ssh-keygen.png`*

#### Configure OpenWRT to Accept the Public Key

```bash
# On OpenWRT (SSH session)
mkdir -p /etc/dropbear
cat >> /etc/dropbear/authorized_keys << 'EOF'
[paste the content of id_ed25519.pub here]
EOF
chmod 600 /etc/dropbear/authorized_keys

# Restart dropbear SSH service
/etc/init.d/dropbear restart
```

> **📸 Screenshot required:** authorized_keys file created and dropbear restarted  
> *File: `screenshots/task4-2-1-authorized-keys.png`*

#### After State — Successful Key-Based Login

```powershell
# From Windows host — login using key (no password prompt)
ssh -i ~/.ssh/id_ed25519 root@192.168.56.2 -p 2222
```

> **📸 Screenshot required:** Successful SSH login using key (no password prompt)  
> *File: `screenshots/task4-2-1-ssh-key-login.png`*

#### Why Key-Based Authentication Is More Secure

| Factor | Password Authentication | Key-Based Authentication |
|--------|------------------------|-------------------------|
| Brute force vulnerability | High — passwords can be guessed | None — 256-bit key is computationally infeasible to brute force |
| Phishing risk | High — users can be tricked into entering passwords | None — private key is never transmitted |
| Credential reuse | High — same password used across systems | None — each key pair is unique |
| Automation | Requires human input | Can be automated securely |
| Lost credential recovery | Password reset needed | Revoke public key from authorized_keys |

Key-based authentication provides **cryptographic proof of identity** rather than relying on a shared secret. Even if an attacker intercepts the SSH traffic, they cannot recover the private key.

---

### Hardening Step 4 — Disable Unnecessary Services

**Security Risk Addressed:** Every running service is a potential attack vector. Services that are not needed provide no business value but increase the attack surface — if a vulnerability is found in an unnecessary service, the system can be compromised even though the service was never intended to be used.

#### Identify Running Services

```bash
# List all running services
/etc/init.d/ status 2>/dev/null | grep -E "running|enabled"
# Or
ps aux
```

> **📸 Screenshot required:** List of running services on OpenWRT  
> *File: `screenshots/task4-2-1-services-list.png`*

#### Disable Telnet (if enabled)

Telnet transmits all data including passwords in **plaintext** — it must be disabled if present.

```bash
# Check if telnet/telnetd is enabled
ls /etc/rc.d/ | grep telnet

# Disable if found
/etc/init.d/telnet stop
/etc/init.d/telnet disable

# Verify it no longer starts
/etc/init.d/telnet status
```

> **📸 Screenshot required:** Telnet disabled — before and after status  
> *File: `screenshots/task4-2-1-telnet-disabled.png`*

**Why disabling unnecessary services improves security:** The principle of minimising the attack surface states that each additional running service introduces potential vulnerabilities (unpatched software bugs, misconfigurations, or protocol weaknesses). Disabling telnet specifically eliminates the risk of credentials and data being intercepted in plaintext over the network. This aligns with NIST SP800-53 control CM-7 (Least Functionality) which requires organisations to configure systems to provide only essential capabilities.

---

## 4.2.2 Capture and Analyse Network Traffic

Network traffic was captured on OpenWRT using **tcpdump** and analysed on the Windows host using **Wireshark**. Two captures were performed — one for HTTP and one for SSH.

---

### Capture 1 — HTTP Traffic

#### Step 1 — Start tcpdump capture on OpenWRT

```bash
# SSH into OpenWRT
ssh root@192.168.56.2 -p 2222

# Capture HTTP traffic on the management interface, save to file
tcpdump -i br-mng -w /tmp/http_capture.pcap port 80

# (Let this run, then from Windows host browse to http://192.168.56.2/)
# Press Ctrl+C after a few seconds to stop capture
```

> **📸 Screenshot required:** tcpdump running and capturing packets  
> *File: `screenshots/task4-2-2-tcpdump-http.png`*

#### Step 2 — Transfer pcap to Windows host

```powershell
# From Windows PowerShell
scp -i ~/.ssh/id_ed25519 -P 2222 root@192.168.56.2:/tmp/http_capture.pcap C:\Users\user\Desktop\http_capture.pcap
```

**pcap file submitted in:** `captures/http_capture.pcap`

#### Step 3 — Open in Wireshark and Identify HTTP Request/Response

Open `http_capture.pcap` in Wireshark on Windows.

Filter used: `http`

**HTTP GET Request identified:**
```
Frame N: [HTTP GET Request]
Source IP:      192.168.56.1  (Windows host)
Destination IP: 192.168.56.2  (OpenWRT web server)
Protocol:       HTTP
Method:         GET
URI:            /
Host:           192.168.56.2
```

**HTTP 200 OK Response identified:**
```
Frame N+1: [HTTP 200 OK Response]
Source IP:      192.168.56.2  (OpenWRT web server)
Destination IP: 192.168.56.1  (Windows host)
Protocol:       HTTP/1.1
Status:         200 OK
Content-Type:   text/html
Body:           Contains: "KarachiPro Consultants", "Pramit Kumar Sah", "12329015", 
                "Hamed Hussain Awad Mohammed", "12324875"
```

> **📸 Screenshot required:** Wireshark showing HTTP GET request and 200 OK response  
> *File: `screenshots/task4-2-2-wireshark-http-request.png`*

> **📸 Screenshot required:** Wireshark packet details showing URL, IPs, and page content  
> *File: `screenshots/task4-2-2-wireshark-http-payload.png`*

#### Step 4 — Visible Information and Security Implications

**Information visible in captured HTTP packets:**

| Visible Field | Value |
|--------------|-------|
| Source IP | 192.168.56.1 (client/Windows host) |
| Destination IP | 192.168.56.2 (web server) |
| HTTP Method | GET |
| Requested URL | http://192.168.56.2/ |
| Web page content | Full HTML including student names, IDs, business info |
| HTTP Headers | User-Agent (browser version and OS), Accept-Language, Connection type |
| Cookies | Any session cookies if present |

**Security Implications of HTTP Interception:**

An attacker positioned between the client and server (Man-in-the-Middle attack) could:

1. **Read all web content** — including any business or client data served over HTTP
2. **Steal session cookies** — allowing session hijacking and impersonation of logged-in users
3. **Capture submitted form data** — if any login forms or client intake forms are submitted over HTTP, credentials and personal information are exposed in plaintext
4. **Modify page content** — inject malicious scripts (XSS) or redirect links to attacker-controlled sites
5. **Profile the client** — User-Agent headers reveal the browser version and operating system, aiding targeted exploitation

**Mitigation:** The website should use **HTTPS (TLS/SSL)** to encrypt all traffic between the client and server. Even if an attacker captures the packets, they would see only encrypted ciphertext with no readable content.

---

### Capture 2 — SSH Traffic

#### Step 1 — Start tcpdump capture on OpenWRT

```bash
# On OpenWRT — capture SSH traffic (while a NEW SSH session is established)
tcpdump -i br-mng -w /tmp/ssh_capture.pcap port 2222
```

Then from a **separate Windows terminal**, initiate an SSH session:
```powershell
ssh -i ~/.ssh/id_ed25519 root@192.168.56.2 -p 2222
# Run a few commands (e.g., ls, ip addr) to generate traffic
# Exit the SSH session
```

Stop tcpdump with Ctrl+C.

> **📸 Screenshot required:** tcpdump running while SSH session is active  
> *File: `screenshots/task4-2-2-tcpdump-ssh.png`*

#### Step 2 — Transfer and Open in Wireshark

```powershell
scp -i ~/.ssh/id_ed25519 -P 2222 root@192.168.56.2:/tmp/ssh_capture.pcap C:\Users\user\Desktop\ssh_capture.pcap
```

**pcap file submitted in:** `captures/ssh_capture.pcap`

Open `ssh_capture.pcap` in Wireshark. Filter: `ssh` or `tcp.port == 2222`

#### Step 3 — SSH Packets Show Encrypted Payload

Wireshark shows SSH packets with:
- Source and destination IPs are visible (network layer — not encrypted)
- TCP port 2222 is visible (transport layer — not encrypted)
- **Payload is encrypted** — shows as binary data with no readable content

> **📸 Screenshot required:** Wireshark showing SSH packets with encrypted/unreadable payload  
> *File: `screenshots/task4-2-2-wireshark-ssh-encrypted.png`*

Example Wireshark SSH packet detail:
```
SSH Protocol
  SSH Version 2
  Encrypted Packet [length: XXX]
  [Payload content: <not decryptable — encrypted with AES-256-CTR>]
```

#### Step 4 — Comparison: HTTP vs SSH Encryption

| Attribute | HTTP (Capture 1) | SSH (Capture 2) |
|-----------|-----------------|----------------|
| Transport encryption | None — plaintext | Yes — SSH uses TLS-equivalent encryption |
| Payload visible? | Yes — full HTML, headers, content | No — binary encrypted data only |
| Username visible? | N/A | No — authentication is encrypted |
| Commands visible? | N/A | No — all session commands encrypted |
| Credentials exposed? | Yes (if login forms used) | No |
| Man-in-the-Middle risk | High | Very low (key fingerprint verification) |

**Why Encrypted Protocols Are Important:**

SSH protects data in transit through **end-to-end encryption** using strong cryptographic algorithms (e.g., AES-256 for data encryption, ED25519 for key exchange). Even if an attacker captures every packet of an SSH session, they cannot read the commands executed, data transferred, or credentials used. This is in stark contrast to HTTP, where all content is visible to any intermediary on the network path.

For a legal/accounting firm like KarachiPro Consultants, encrypted communications are essential because:
- Remote administration commands (via SSH) could reveal system configuration and security measures
- Client data files transferred over the network must not be interceptable
- Compliance with data protection principles requires encryption of sensitive data in transit

---

## 4.3 Risk Assessment and Security Controls

## 4.3.1 Cyber Security Risk Assessment

A mini cyber security risk assessment was conducted for KarachiPro Consultants using the **TVAMatrix methodology** as provided in the unit. The full TVAMatrix spreadsheet is in the repository.

**Risk Assessment file:** `docs/TVAMatrix-KarachiPro.xlsx`

> **📸 Screenshot required:** TVAMatrix spreadsheet — full view (take multiple screenshots if needed)  
> *File: `screenshots/task4-3-1-tvamatrix.png`*

### Summary of Risk Assessment

The risk assessment considered the following **asset types:**

| Asset Type | Examples Identified |
|-----------|-------------------|
| Hardware | OpenWRT router, staff workstations (×8), web server, network switch, ISP modem |
| Software | OpenWRT firmware, uhttpd web server, Windows 11 OS, accounting software, legal document management system |
| Data | **Client financial records**, **Legal case documents**, **Staff personal data**, **Business contracts**, Client contact information, Website content |
| Services | Internet access, remote administration (SSH), public website, internal file sharing |
| People | Managing Partner, Senior Lawyers, Accountants, IT Support Officer, Receptionist, Office Manager |

### Threats Considered (8 of 12)

| # | Threat Category | Relevant Assets | Notes |
|---|----------------|----------------|-------|
| 1 | Unauthorised Access | Client financial records, admin interface | Weak/default credentials risk |
| 2 | Malware/Ransomware | Staff workstations, file server | Phishing emails targeting accounting staff |
| 3 | Network Intrusion | All networked assets | Unsecured firewall rules |
| 4 | Data Interception | Client data over HTTP | Plaintext transmission risk |
| 5 | Social Engineering | Staff, credentials | Phishing targeting firm staff |
| 6 | Denial of Service | Web server, internet service | Router/web server flood attacks |
| 7 | Physical Access | Router hardware, workstations | Unlocked server room |
| 8 | Insider Threat | Client records, legal documents | Disgruntled or negligent staff |

### Highest Risk Data Asset — Client Financial Records

Based on the TVAMatrix ratings, **Client Financial Records** received the **highest overall risk rating** due to:
- **High asset value** — financial data is extremely sensitive; loss or exposure causes financial and reputational damage
- **High threat likelihood** — accounting firms are specifically targeted by ransomware operators and financial fraudsters
- **High vulnerability** — currently transmitted and accessed without encryption (HTTP), and stored on workstations without enforced access controls

---

## 4.3.2 Recommended Security Controls

The three security controls below are recommended for **Client Financial Records** — the highest-risk data asset identified.

---

### Control 1 — AC-3: Access Enforcement / Role-Based Access Control (RBAC)

**NIST SP800-53 Reference:** AC-3 (Access Enforcement)

**How it reduces risk:**
Client financial records should only be accessible by Accountants and the Managing Partner — not by administrative staff, receptionists, or lawyers with no financial responsibilities. RBAC enforces this by assigning permissions based on job roles rather than individual users, preventing unauthorised staff from reading, copying, or modifying financial files.

**Implementation in KarachiPro scenario:**
- Configure the file server (or document management system) to create access groups: `accounting-group` (Accountants + Managing Partner) and `legal-group` (Lawyers)
- Client financial records folders are accessible only to `accounting-group`
- Windows domain (Active Directory) can enforce these group policies across all 8 workstations
- The OpenWRT firewall rules already restrict access between network segments — combined with RBAC, even a staff member on the same network cannot access restricted files without proper credentials
- The IT Support Officer manages user accounts but should **not** be in `accounting-group` (separation of duties)

**Disadvantages:**
- Setup and maintenance of RBAC requires time from IT Support
- If roles change (staff promotion, new hires), access lists must be promptly updated — delays create gaps
- Staff may find access restrictions inconvenient when covering for absent colleagues, potentially leading to workaround behaviour (e.g., sharing login credentials)

---

### Control 2 — SC-8: Transmission Confidentiality and Integrity (Enforce HTTPS/TLS)

**NIST SP800-53 Reference:** SC-8 (Transmission Confidentiality and Integrity)

**How it reduces risk:**
As demonstrated in the HTTP traffic capture (Section 4.2.2), client financial data transmitted over HTTP is visible to any network observer. Enforcing **HTTPS (TLS 1.3)** encrypts all data in transit, ensuring that even if packets are intercepted (e.g., via ARP spoofing on the internal LAN), the content is unreadable.

**Implementation in KarachiPro scenario:**
- Install a TLS certificate on the uhttpd web server on OpenWRT (or the dedicated web server in the production DMZ at 15.10.1.10)
- Configure uhttpd to redirect all HTTP (port 80) requests to HTTPS (port 443): `option redirect_https 1` in `/etc/config/uhttpd`
- For the internal document management system used by accounting staff, enforce HTTPS-only access (disable HTTP)
- The firewall rule from Section 4.1.3 (blocking port 80) complements this control — once HTTPS is enabled, port 80 should be permanently closed or set to redirect only
- Use **Let's Encrypt** (via acme.sh on OpenWRT) for free, automatically-renewed TLS certificates for the public-facing website

**Disadvantages:**
- TLS certificates must be renewed (typically every 90 days for Let's Encrypt) — failure to renew causes the website to show security warnings, disrupting client access
- HTTPS adds minor computational overhead to the router/web server — negligible on modern hardware but relevant on older OpenWRT devices
- Internal clients may need to trust a self-signed certificate if using internal HTTPS, requiring configuration on each workstation

---

### Control 3 — AU-2: Event Logging and Audit (Security Logging and Monitoring)

**NIST SP800-53 Reference:** AU-2 (Event Logging)

**How it reduces risk:**
Without logging, unauthorised access to client financial records may go undetected for weeks or months. Comprehensive event logging creates an audit trail that enables detection of suspicious activity (e.g., an accountant accessing records at 2am), supports incident response, and serves as evidence in legal proceedings.

**Implementation in KarachiPro scenario:**
- Enable OpenWRT's system log (`syslog`) to record: all SSH login attempts (success and failure), firewall rule triggers, and web server access
- Configure logging of all file accesses on the document management system — record: username, timestamp, file name, operation (read/write/delete)
- Forward logs to a centralised syslog server (can be a dedicated VM on the management VLAN at 75.30.1.20) to prevent tampering
- Set up alerts for: 5+ failed SSH login attempts in 10 minutes (potential brute force), access to client financial records outside business hours (8am–6pm PKT), any changes to firewall rules

Relevant to our setup: The firewall rules configured in Section 4.1.3 (blocking/allowing HTTP, SSH port change, ICMP rules) should all be logged. In OpenWRT iptables, logging is added with:
```bash
iptables -I INPUT -i br-mng -j LOG --log-prefix "FIREWALL: " --log-level 4
```

**Disadvantages:**
- Log storage requires ongoing disk space management — logs must be rotated and archived
- Excessive logging can generate alert fatigue, causing administrators to ignore important events among false positives
- Logs themselves are sensitive — if an attacker gains access to the syslog server, they can see which accounts are active, what files are accessed, and potentially erase evidence of their intrusion (log tampering). Logs must be protected with strong access controls and write-once storage where possible

---

*Document version: 1.0 | Last updated: May 2026*
