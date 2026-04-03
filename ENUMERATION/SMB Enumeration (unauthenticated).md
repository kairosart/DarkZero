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


3. Enumerate users

```bash
netexec smb <MACHINE IP> -u 'john.w' -p 'RFulUtONCOL!' -d darkzero.htb --users
```

![[Screenshot_2026-04-01_16-49-34.png]]
 
 - The results are standard, revealing default shares (`ADMIN$`, `C$`, `IPC$`, `NETLOGON`, `SYSVOL`) and a small number of users. 
 - The user `john.w` has no special permissions and can only read the default shares.

4. Discovering the Linked Server
	Use the client’s built-in *enum_links* command.

```sql
enum_links
```

![[Screenshot_2026-04-01_10-37-11.png]]

This is the pivot point. There is a linked server pointing to `DC02.darkzero.ext`. Furthermore, any query we send through this link from our `john.w` session will execute on `DC02` under the security context of the remote login `dc01_sql_svc`.





**Next step:** [[Lateral Movement via MSSQL Linked Server]]
