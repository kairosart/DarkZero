
### 1. Confirm ADCS is Running

```powershell
net start | findstr "Certificate"
certutil -config - -ping
```

### 2. From Kali — Enumerate ADCS Vulnerabilities

```bash
netexec ldap 10.129.12.60 -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb -M adcs
```

### 3. Check for Vulnerable Certificate Templates

bash

```bash
certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' -dc-ip 10.129.12.60 -vulnerable -stdout
```

### 4. On the DC02 Shell

powershell

```powershell
certutil -catemplates
```