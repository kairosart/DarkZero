#Attacking_machine 

 1. Validate Credentials

```bash
netexec smb <MACHINE IP> -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb
```

![[Screenshot_2026-03-22_20-16-39.png]]

2. Enumerate Shares with Creds

```bash
netexec smb <MACHINE IP> -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --shares
```

![[Screenshot_2026-03-22_20-25-31.png]]


3. Full LDAP Dump users

```bash
netexec ldap 10.129.12.60 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --users
```

![[Screenshot_2026-03-22_20-30-47.png]]
 
 4. Full LDAP Dump groups

```bash
netexec ldap 10.129.12.60 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --groups
```

![[Screenshot_2026-03-23_10-45-18.png]]


---

## 🗂️ DarkZero HTB — Progress Summary

### 🎯 Target

|Detail|Value|
|---|---|
|IP|10.129.12.60|
|Hostname|DC01.darkzero.htb|
|Domain|darkzero.htb|
|OS|Windows Server 2025|
|Role|Domain Controller|

---

### 🔑 Credentials Found

|User|Password|Source|
|---|---|---|
|john.w|RFulUtONCOL!|HTB machine info (given)|
|svc_sql|—|Service account on DC02|

---

### 🔍 Recon Results

- SMB null session authenticates but **no readable shares**
- LDAP anonymous bind **blocked**
- RID brute / lookupsid **blocked**
- No **Kerberoastable** or **ASREPRoastable** accounts
- **MSSQL (1433)** open on DC01 — key attack surface


**Next step:** [[Lateral Movement via MSSQL Linked Server]]
