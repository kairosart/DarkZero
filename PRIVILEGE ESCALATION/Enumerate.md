🎉 Shell on *DC02* as *darkzero-ext\svc_sql*. Now let's enumerate and escalate.

#powershell
### Check Your Privileges

```powershell
whoami /priv
whoami /groups
net localgroup administrators
```

![[Screenshot_2026-03-23_20-00-50.png]]


![[Screenshot_2026-03-23_20-00-12.png]]

![[Screenshot_2026-03-23_20-03-34.png]]

🔥 **Very interesting!** `svc_sql` is not a local admin, but notice this group:

> `BUILTIN\Certificate Service DCOM Access`

This means Active Directory Certificate Services (*ADCS*) is running on *DC02*! This opens up ESC (Escalation) attacks.

**Next step:** [[Escalation attack]]