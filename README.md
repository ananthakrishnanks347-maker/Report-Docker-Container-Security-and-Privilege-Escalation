<div align="center">

# 🐳 Docker Socket Escape — LFI to Root Privilege Escalation

**Local Privilege Escalation through Insecure Container Socket Volume Exposure**

![Severity](https://img.shields.io/badge/Severity-Critical-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Platform](https://img.shields.io/badge/Platform-Linux%20%2F%20Ubuntu-blue)
![Category](https://img.shields.io/badge/Category-Internal%20Pentest-orange)

*Submitted as the project for the Advanced Diploma in Cyber Defence*
**RedTeam Hacker Academy, Kochi** · Guided by Mr. Saurav Sha · By **Ananthakrishnan K S**

</div>

---

## 📌 TL;DR

A web app's unsanitized `?page=` parameter enabled **Local File Inclusion**, which was used to steal a user's SSH private key. That user belonged to the `docker` group — and because the Docker daemon socket runs as root, that group membership was enough to spawn a container, mount the entire host filesystem into it, and `chroot` out to a **root shell on the host**.

```
LFI (?page=)  →  Leak /etc/passwd + SSH private key  →  SSH foothold (vnx)
     →  LinPEAS confirms docker group membership  →  Abuse docker.sock
     →  Mount host / into container  →  chroot escape  →  ROOT
     →  Flag captured  →  Persistence via credential reset
```

**Impact:** Full host compromise, arbitrary file read/write, persistent admin access via credential reset.

---

## 🎯 Objective & Scope

| | |
|---|---|
| **Target host** | `192.168.1.6` — Ubuntu 20.04 LTS (VNX) |
| **Attacking host** | `192.168.1.11` — Kali Linux |
| **Entry point** | Web app on port 80 (`/ctf/`) |
| **Root cause** | Writable Docker socket + unsanitized file-include parameter |

> ℹ️ The target IP is referenced inconsistently in the source material (`.6` in the initial scans/SSH session, `.9` in the `gobuster`/LFI commands) — see the note at the top of the full report.

---

## 🛠 Tools Used

| Tool | Purpose |
|---|---|
| `arp-scan` | Host discovery on the local subnet |
| `nmap` | Service and version enumeration |
| `gobuster` | Web directory brute-forcing |
| `curl` | Payload delivery and file exfiltration via LFI |
| `LinPEAS` | Automated local privilege escalation enumeration |
| `docker` (pull / save / load) | Offline container image sideloading |
| GTFOBins (`docker`) | Reference for the `chroot` escape technique |

---

## 🔄 Methodology

```
+------------------------+     +------------------------+     +------------------------+
| Information Gathering | --> |  Service Enumeration   | --> |      Penetration       |
+------------------------+     +------------------------+     +------------------------+
                                                                          |
                                                                          v
+------------------------+     +------------------------+     +------------------------+
|     House Cleaning     | <-- |    Maintaining Access  | <-- |    Post-Exploitation   |
+------------------------+     +------------------------+     +------------------------+
```

1. **Information Gathering** — subnet mapping to find the live target
2. **Service Enumeration** — port/service scan, directory brute-force, group/permission checks
3. **Penetration** — exploit the LFI, steal credentials, escalate via the Docker socket
4. **Maintaining Access** — reset a local account password for persistent GUI login
5. **Post-Exploitation** — flag retrieval and impact verification
6. **House Cleaning** — remove staged tools and artifacts from the target

---

## 📋 Findings & Remediation Summary

| Ref | Finding | Severity | Remediation |
|---|---|:---:|---|
| 3.2 | Local File Inclusion via `?page=` parameter | 🔴 Critical | Whitelist allowed file paths; disable dynamic path concatenation |
| 3.3 | SSH private key exposed via web-accessible path | 🟠 High | Relocate keys outside web roots; enforce passphrase-protected keys / MFA |
| 3.6 | Writable Docker socket / unprivileged user in `docker` group | 🟠 High | Remove non-admin accounts from `docker` group; use rootless container runtime |

---

## 📖 Full Writeup

The complete walkthrough — every step from initial recon through root shell, flag capture, and persistence, with all 20 screenshots — is documented in:

### ➡️ **[`Full-Report.md`](./Full-Report.md)**

---

## 🧠 Skills Demonstrated

`Web Application Testing` · `Local File Inclusion Exploitation` · `Linux Privilege Escalation` · `Container Security` · `Docker Socket Abuse` · `Automated Enumeration (LinPEAS)` · `Persistence Techniques` · `Technical Report Writing`

---

## ⚠️ Disclaimer

This assessment was performed against an **isolated, purpose-built lab environment** as part of a structured cybersecurity training program. All techniques are documented for educational purposes. Do not use these techniques against systems you do not own or have explicit written authorization to test.

---

<div align="center">

**Ananthakrishnan K S** — Aspiring Penetration Tester / Security Researcher
[GitHub](https://github.com/ananthakrishnanks347-maker) · [TryHackMe](https://tryhackme.com/p/Ananthakrishnank.s) · [LinkedIn](https://www.linkedin.com/in/ananthakrishnan-ks-)

</div>
