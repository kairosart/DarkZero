Remember you have a tunnet through DC2  to DC1.

#Attacking_machine 
- check if john.w can write to `darkzero-ext$` by attempting to set an SPN on it (which would confirm GenericWrite):

```bash
LDAPTLS_REQCERT=never proxychains ldapsearch -x -H ldaps://172.16.20.1:636 \
  -D 'darkzero\john.w' -w 'RFulUtONCOL!' \
  -b "DC=darkzero,DC=htb" \
  "(objectClass=domain)" \
  ms-DS-MachineAccountQuota
```

![[Screenshot_2026-04-01_09-06-59.png]]

**MachineAccountQuota = 10** — john.w can create up to 10 machine accounts ✅

- Check if john.w created *darkzero-ext$* (would mean he owns it):

```bash
LDAPTLS_REQCERT=never proxychains ldapsearch -x -H ldaps://172.16.20.1:636 \
  -D 'darkzero\john.w' -w 'RFulUtONCOL!' \
  -b "DC=darkzero,DC=htb" \
  "(sAMAccountName=darkzero-ext$)" \
  msDS-CreatorSID
```

![[Screenshot_2026-04-01_09-11-10.png]]

**msDS-CreatorSID empty** — `darkzero-ext$` was created by an admin, not john.w.

Here's my reasoning:

- `msDS-CreatorSID` is populated when a **non-admin** creates a machine account via MAQ
- When an **admin** creates a computer object, `msDS-CreatorSID` is typically **not set**
- Since the field came back empty, it suggests an admin created it

However I could be wrong — it's also possible that:

- The attribute exists but john.w doesn't have **read permission** on it
- It was deliberately cleared
- Some other edge case

The only way to confirm who owns `darkzero-ext$` is to read its ACL (`nTSecurityDescriptor`), which we already know john.w can't read on most objects.

**Bottom line:** Don't let that stop you — the RBCD attack via MAQ is still valid regardless. Try the `impacket-addcomputer` step and see if it succeeds. That's the real test of what john.w can do.

**Next step:** [[Attack Path - RBCD via MachineAccountQuota]]