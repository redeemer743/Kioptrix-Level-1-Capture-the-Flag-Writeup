# OpenFuck Exploit Notes (CVE-2002-0656)

## 📍 Local Kali Linux Location
The original exploit code can be mirrored or found locally on Kali Linux at:
* **Exploit-DB Path:** `/usr/share/exploitdb/exploits/unix/remote/764.c`
* **Searchsploit Command:** `searchsploit -m unix/remote/764.c`

## 🛠️ Compilation Details
When compiling on a modern compiler (like GCC on Kali 2026), the script will trigger several deprecation warnings due to structural changes in newer OpenSSL 3.x libraries. 

### Compilation Command:
```bash
gcc -o OpenFuck OpenFuck.c -lcrypto
```

### Expected Warnings:
* `warning: 'RC4' is deprecated`
* `warning: 'MD5_Init' is deprecated`
* `warning: 'EVP_PKEY_get1_RSA' is deprecated`

*Note: These are warnings, not fatal errors. The binary compiles successfully and generates the executable.*
