# Screenshots — COIT20246 Project

This folder contains all screenshots for the project report. Screenshots must be taken from your actual VirtualBox/OpenWRT setup.

## Required Screenshots Checklist

### Task 4.1.2 — Network Setup
- [ ] `task4-1-2-ip-addr.png` — Output of `ip addr` on OpenWRT showing all interfaces and IPs
- [ ] `task4-1-2-website-browser.png` — KarachiPro website loading in Windows host browser
- [ ] `task4-1-2-netstat.png` — `netstat -tlnp` showing uhttpd on port 80
- [ ] `task4-1-2-ping-test.png` — Successful ping from Windows to 192.168.56.2

### Task 4.1.3 — Firewall Rules
- [ ] `task4-1-3-http-before-block.png` — Website accessible before block
- [ ] `task4-1-3-http-block-rule.png` — iptables DROP rule for port 80
- [ ] `task4-1-3-http-after-block.png` — Website inaccessible after block
- [ ] `task4-1-3-http-allow.png` — iptables ACCEPT rule; website loading again
- [ ] `task4-1-3-ssh-port22-success.png` — Successful SSH on port 22
- [ ] `task4-1-3-ssh-port-change.png` — uci set commands for port 2222
- [ ] `task4-1-3-ssh-port2222-success.png` — SSH working on 2222, failing on 22
- [ ] `task4-1-3-icmp-before-block.png` — Ping working before block
- [ ] `task4-1-3-icmp-block-rule.png` — iptables ICMP DROP rule
- [ ] `task4-1-3-icmp-after-block.png` — Ping timing out after block
- [ ] `task4-1-3-icmp-re-enabled.png` — Ping working again
- [ ] `task4-1-3-luci-before.png` — LuCI accessible on port 81
- [ ] `task4-1-3-luci-rule.png` — iptables management interface restriction
- [ ] `task4-1-3-luci-restricted.png` — LuCI blocked / allowed correctly

### Task 4.2.1 — Security Hardening
- [ ] `task4-2-1-passwd-before.png` — Login before password change
- [ ] `task4-2-1-passwd-change.png` — `passwd` command running
- [ ] `task4-2-1-passwd-after.png` — SSH requiring new password
- [ ] `task4-2-1-shadow-file.png` — `/etc/shadow` showing hash (blur actual hash if needed)
- [ ] `task4-2-1-ssh-password-login.png` — SSH with password (before key setup)
- [ ] `task4-2-1-ssh-keygen.png` — `ssh-keygen` output
- [ ] `task4-2-1-authorized-keys.png` — authorized_keys file created
- [ ] `task4-2-1-ssh-key-login.png` — SSH login using key (no password)
- [ ] `task4-2-1-services-list.png` — List of running services
- [ ] `task4-2-1-telnet-disabled.png` — Telnet disabled (before/after)

### Task 4.2.2 — Traffic Captures
- [ ] `task4-2-2-tcpdump-http.png` — tcpdump running for HTTP capture
- [ ] `task4-2-2-wireshark-http-request.png` — Wireshark HTTP GET and 200 OK
- [ ] `task4-2-2-wireshark-http-payload.png` — Wireshark showing HTML content with names/IDs
- [ ] `task4-2-2-tcpdump-ssh.png` — tcpdump running for SSH capture
- [ ] `task4-2-2-wireshark-ssh-encrypted.png` — Wireshark showing encrypted SSH payload

### Task 4.3.1 — Risk Assessment
- [ ] `task4-3-1-tvamatrix.png` — TVAMatrix spreadsheet screenshot (take multiple if large)

### Task 4.4 — Reflection
- [ ] `task4-4-commits.png` — GitHub repository commit history showing both members

---

## Screenshot Tips

- Use **Windows Snipping Tool** (Win+Shift+S) or **Print Screen** for screenshots
- Make sure the terminal text is **large enough to read** — zoom in (Ctrl+scroll) before capturing
- For SSH terminal screenshots: set terminal font to at least 14pt
- Wireshark screenshots: expand the relevant protocol tree before screenshotting
- Label each file **exactly as listed above** so they match the Markdown references

---

*Last updated: May 2026*
