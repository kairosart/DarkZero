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


3. Full LDAP Dump

```bash
netexec ldap 10.129.12.60 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --users
netexec ldap 10.129.12.60 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --groups
```

![[Screenshot_2026-03-22_20-30-47.png]]
 
 4. Bloodhound Collection

```bash
bloodhound-python -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb -dc DC01.darkzero.htb -ns 10.129.12.60 -c All
```

![[Screenshot_2026-03-22_20-33-58.png]]