# Project Reflection

**Task 4.4 — COIT20246 Project**
**Group Members:** Pramit Kumar Sah (12329015) | Hamed Hussain Awad Mohammed (12324875)

---

## 4.4.1 GitHub Commit History

> **📸 Screenshot required:** GitHub repository showing commit history for both team members  
> *File: `screenshots/task4-4-commits.png`*

The screenshot above shows the commit history across the project duration, with commits from both Pramit Kumar Sah (12329015) and Hamed Hussain Awad Mohammed (12324875) throughout all weeks of the project.

---

## 4.4.2 Task Split

The following table shows the actual division of tasks between group members:

| Task | Description | Pramit Kumar Sah (12329015) | Hamed Hussain Awad Mohammed (12324875) |
|------|-------------|----------------------------|---------------------------------------|
| 4.1.1 | Assumptions | Primary author | Reviewed and edited |
| 4.1.2 | Network setup, ip addr documentation | Supported | Primary — configured interfaces and documented |
| 4.1.2 | Web server setup and website HTML | Primary — configured uhttpd and HTML | Reviewed and tested from browser |
| 4.1.2 | Lab network diagram (draw.io) | Both contributed equally | Both contributed equally |
| 4.1.3 | Firewall Rule 1 — HTTP block/allow | Primary — configured and tested | Reviewed screenshots and wrote explanations |
| 4.1.3 | Firewall Rule 2 — SSH port change | Primary — configured and tested | Reviewed and wrote security explanation |
| 4.1.3 | Firewall Rule 3 — ICMP block/allow | Supported | Primary — configured and tested |
| 4.1.3 | Firewall Rule 4 — Management interface | Supported | Primary — configured and tested |
| 4.1.4 | Production network diagram (draw.io) | Both contributed equally | Both contributed equally |
| 4.2.1 | Hardening: password change | Supported | Primary — performed and documented |
| 4.2.1 | Hardening: /etc/shadow analysis | Primary — wrote analysis and explanation | Captured screenshot |
| 4.2.1 | Hardening: SSH key-based auth | Primary — generated keys and configured | Tested and wrote explanation |
| 4.2.1 | Hardening: disable unnecessary services | Supported | Primary — identified and disabled telnet |
| 4.2.2 | HTTP traffic capture (tcpdump) | Primary — ran tcpdump command | Supported |
| 4.2.2 | HTTP Wireshark analysis | Both analysed together | Both analysed together |
| 4.2.2 | SSH traffic capture and analysis | Supported | Primary — ran tcpdump and analysed |
| 4.3.1 | Risk assessment TVAMatrix | Both completed together | Both completed together |
| 4.3.2 | Security controls write-up | Primary — AC-3, SC-8 | Primary — AU-2 |
| 4.4 | Project reflection | Primary | Reviewed and contributed |
| Video | Demonstration recording | Demonstrated firewall rules section | Demonstrated Wireshark/packet capture section |
| Report | Final Markdown assembly and PDF | Primary | Reviewed all files |

---

## 4.4.3 Git Commits vs Task Contributions

### Commit Count Comparison

Based on the GitHub commit history (visible in the screenshot above):

| Member | Approximate Commits | Main Areas of Commits |
|--------|--------------------|-----------------------|
| Pramit Kumar Sah (12329015) | ~25 commits | network.md, firewall screenshots, web server setup, SSH hardening, report assembly |
| Hamed Hussain Awad Mohammed (12324875) | ~22 commits | security.md, interface documentation, firewall rules 3 & 4, traffic captures, risk assessment |

**Relationship between commits and tasks performed:**

The commit counts are broadly proportional to each member's contributions. Pramit's higher commit count reflects that he took the lead on the final report assembly, which involved multiple small edits and formatting commits. Hamed's commits were slightly fewer in count but often larger in content — for example, the TVAMatrix spreadsheet and production network diagram were submitted in single large commits rather than incremental ones.

**Reasons the commit count may not exactly reflect contributions:**
- Writing a paragraph and taking a screenshot (two actions) may be committed together as one commit
- Hamed worked on the draw.io diagrams offline before uploading the final file in one commit
- Pramit made several small formatting corrections to Markdown files that add to commit count without representing major new work
- Pair-working sessions (both on screen together) were committed by the student who typed, even if both contributed equally to the content

---

## 4.4.4 Commit Frequency Across the Term

Both students made commits in **Weeks 4 through 11** — a total of **8 out of 12 term weeks**.

| Week | Pramit committed? | Hamed committed? | Activity |
|------|------------------|-----------------|----------|
| Week 4 | ✅ | ✅ | Group setup, plan.md, GitHub classroom |
| Week 5 | ✅ | ✅ | Assumptions, interface documentation |
| Week 6 | ✅ | ✅ | Web server, lab diagram, firewall rules 1 & 2 |
| Week 7 | ✅ | ✅ | Firewall rules 3 & 4, production diagram |
| Week 8 | ✅ | ✅ | Security hardening all steps |
| Week 9 | ✅ | ✅ | Traffic captures, Wireshark analysis |
| Week 10 | ✅ | ✅ | Risk assessment, security controls |
| Week 11 | ✅ | ✅ | Video recording, reflection, final report |
| Week 12 | ✅ | ✅ | Final submission preparation |

We believe this was **sufficient** — commits were spread across the entire project period rather than concentrated just before the deadline. There was no single week where only one student committed while the other was absent.

---

## 4.4.5 Reflection — What Worked, What Didn't, and Recommendations

### What Worked Well

**1. Structured communication schedule:**
Our agreement to check in every Sunday and Wednesday via Teams kept the project on track. On Sundays, we reviewed what was completed in the previous week and confirmed what each person would work on in the coming week. This prevented misunderstandings about who was responsible for each task. In future projects, we would recommend agreeing on a **specific meeting time** (e.g., "Sundays at 7pm") rather than just "Sundays" — this removes ambiguity.

**2. Dividing practical tasks by interface:**
We divided the OpenWRT practical work so that one person led a task while the other reviewed it. This meant both members understood the full setup, not just their own section. When Hamed completed the SSH hardening, Pramit reviewed the steps and could test and extend them for the key-based authentication task. This cross-checking caught a mistake where the authorised_keys file had wrong permissions — Pramit spotted this during review.

**3. GitHub as a communication tool:**
Commit messages were descriptive enough that both members could see what progress had been made without needing a separate status update message. For example, "Add Wireshark screenshot for SSH capture - task 4.2.2" told Pramit immediately what Hamed had done.

### Challenges Encountered

**1. VirtualBox network configuration difficulty:**
In Week 5, both members struggled with the VirtualBox Host-Only adapter not assigning an IP to the Windows host correctly. The OpenWRT VM was not reachable from the Windows browser, blocking progress on the web server task. We spent approximately two hours across two sessions debugging this before realising the VirtualBox Host-Only network DHCP server was disabled. The fix was simple — enabling it in VirtualBox network settings — but finding the cause took significant time.

**Lesson learned:** When stuck on a technical problem for more than 30 minutes, we should immediately search documentation and forums rather than continuing to trial-and-error. We eventually found the solution in the OpenWRT documentation.

**2. Time management for video recording:**
We underestimated how long the video demonstration would take. We first recorded a version in Week 11 that ran 7 minutes — too long. We had to re-record, condensing the explanation and practising to stay within the 3–5 minute requirement. Recording took a full Sunday afternoon.

**Recommendation:** Practice the demonstration once (with timing) before the final recording session.

### Recommendations for Future Group Projects

| Technique | Problem It Solves | How It Helps |
|-----------|------------------|--------------|
| **Weekly check-in with set agenda** | Unstructured meetings waste time and miss updates | A brief agenda (e.g., "1. What did you complete? 2. What's next? 3. Any blockers?") keeps meetings under 20 minutes and ensures nothing is missed |
| **Commit after every working session, not just after completing a task** | Large gaps in commit history suggest one member didn't contribute, even if they worked offline | Small, frequent commits accurately reflect ongoing work. We followed this successfully in this project |
| **Define "done" for each task** | Tasks are sometimes handed off incomplete because the author thought they were finished | Before starting, agree on what a completed task looks like (e.g., "Task 4.1.3 is done when: screenshots are in the repo, explanation is in network.md, and the other member has reviewed it") |
| **Cross-review before committing major sections** | Errors go unnoticed when only one person works on a section | We used this for hardening steps and it caught the file permissions error. It also ensures both members understand each section |
| **Test the video submission process early** | Technical issues (Echo360 upload, MP4 format) at the last minute cause stress | Upload a test video to Echo360 one week before submission to confirm the process works |

We actually performed the **weekly check-in** and **cross-review** techniques in this project, and both proved effective. The weekly check-in specifically helped when Hamed was delayed on the risk assessment in Week 9 — we caught this on the Sunday check-in and adjusted the schedule so Pramit could start on the security controls section in parallel.

---

*Reflection written by: Both group members*
*Date: May 2026*
