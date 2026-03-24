
### 1. Confirm ADCS is Running

```powershell
net start | findstr "Certificate"
certutil -config - -ping
```

![[Screenshot_2026-03-24_10-53-48.png]]
### 2. From Kali — Enumerate ADCS Vulnerabilities

```bash
netexec ldap <MACHINE IP> -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb -M adcs
```

### 3. Check for Vulnerable Certificate Templates

bash

```bash
certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' -target DC01.darkzero.htb -dc-ip <MACHIINE IP> -vulnerable -stdout
```

![[Screenshot_2026-03-24_11-13-43.png]]

No vulnerable certificate templates found. But useful info — the CA is at `DC01.darkzero.htb` and there's a second IP `172.16.20.1` visible in the timeout error which suggests an internal network.

> [!Key Observation]
> The timeout to `172.16.20.1:445` means DC02 has a second network interface.

### 4. On the DC02 Shell

Let's explore.

```powershell
ipconfig /all
```

![[Screenshot_2026-03-24_11-27-10.png]]

🔍 *DC02* is on an internal network *172.16.20.0/24* with *DC01* at 172.16.20.1 as the gateway!
This means *DC02* bridges your *HTB network* and the internal network. We need to pivot through *DC02* to reach *DC01* internally.


---

### Where you are now?
![[darkzero_network_schema.svg|968]]
Here's the full network topology. Key takeaways:

- **Kali** reaches **DC01** directly over the HTB VPN via MSSQL
- **DC01** is also the internal gateway at `172.16.20.1`, bridging into the `172.16.20.0/24` network
- **DC02** sits inside that internal network at `172.16.20.2`, only reachable via the linked server from DC01
- The reverse shell comes back from DC02 to Kali over the VPN through DC01 as the gateway
- **Next step** is escalating from `svc_sql` on DC02 toward Domain Admin on DC01 — ADCS is the likely path 🎯

**Next step:** [[Pivot through *DC02* to reach *DC01* internally]]