
If a server sends out SMB connections, you can use abuse NTLM relaying to gain a foothold from these SMB connections. This is an example of how NTLM relaying works and how a Net-NTLM session is created. To exploit the network, we will need to adjust the attack from our research.  

To begin relaying hashes, we need first to understand how hashes would generally be abused. We will demonstrate two tools that are usually used: Responder, [https://github.com/lgandx/Responder](https://github.com/lgandx/Responder) and NTLMRelayX, [https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py)  

## Responder

The first tool we will be looking at is Responder. From the Responder GitHub, *"Responder is a LLMNR, NBT-NS and MDNS poisoner, with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server supporting NTLMv1/NTLMv2/LMv2, Extended Security NTLMSSP and Basic HTTP authentication."* We can use Responder in our situation to poison LLMNR, Netbios, and DNS and then capture the response from the server.  

To begin poisoning requests, we will need to turn off SMB in the Responder configuration as NTLMRelayX will be handling SMB. Find an example command used below.  

Command used: `sudo sed -i 's/SMB = On/SMB = Off/' /etc/responder/Responder.conf`

You can also manually edit the configuration file and turn off SMB.  

Now that SMB is off, we can start responder poisoning across our network interface. Find syntax to start Responder below.  

Syntax: `sudo python Responder.py -I <Interface>`

Responder is now poisoning requests across the network, and we can begin relaying them.  

> [!note]
> It is not necessary to use Responder when attempting to Remotely NTLMRelay. Responder should be used for poisoning a local network, not a remote network. Using Responder is **not** required to complete Holo.


## NTLMRelayX

The second tool we will be looking at is NTLMRelayX, part of the Impacket suite. From the Impacket GitHub, *"This module performs the SMB Relay attacks originally discovered by cDc extended to many target protocols (SMB, MSSQL, LDAP, etc). It receives a list of targets, and for every connection received, it will choose the next target and try to relay the credentials. Also, if specified, it will first try to authenticate against the client connecting to us."*  

We can use it against a specified protocol to relay inbound sessions. Find syntax for starting NTLMRelayX below.  

Syntax: `ntlmrelayx.py -t ldap://<IP> -smb2support --escalate-user <user>`

This is an example of creating a Net-NTLM session. When a valid SMB session is received, NTLMRelayX will act as a proxy and send a challenge to exploit the target system.  

Now that we understand how an NTLM relay works and how a Net-NTLM session is created, we can move on to remote NTLM relaying.

> [!info]
> In order for these attacks to work, it is important for SMB signing to be disabled.


---

# Your job

## The scenario so far

There is a targeted subnet `10.200.X.0/24` where there are 3 machines.

- `10.200.X.35` : 
	- A fully compromised Windows machine with Administrator access. 
	- A meterpreter session on this machine.
- `10.200.X.30` : This is the Domain Controller. One of the potential victims.
- `10.200.X.32` : Another machine on the network. One of the potential victims.
- A Kali Linux attacking machine.

	![[Task 46 - Now you see me, now you don't-20241121143522528.webp]]


## What’s the PLAN ?

1. Discover which of the potential victims have smb signing disabled. It’s required because while we relay the NTLM Session, if smb signing is enabled on the targeted host it will identify that these session wasn’t signed by us, so it will flag the session as malicious. 
2. Prepare a compromised server’s SMB port for your use. To do that shut down every service on the SMB port so that it becomes free for us.
3. Start the `ntlmrelayx` tool from impacket that will actually relay the session. 
4. Set up a remote port forward that will forward the traffic from `10.200.X.35:445` to our Attack machines port 445 with `-socks` option.

This setup will forward any connection that comes to the compromised server’s port 445 `10.200.95.X:445` to your Attack Machine’s port 445. Use `ntlmrelayx` tool to forward the connection back to the victim machine(s) and thus complete the Hash Relay.


## Exploiting

You can use `nmap`:

```
$ nmap 10.200.X.30 -p 445 -sC -sV -sT -Pn
```

![[Task 46 - Now you see me, now you don't-20241121144700066.webp]]

or `crackmapexec`:

```
$ crackmapexec smb 10.200.X.30 10.200.X.32
```

![[Task 46 - Now you see me, now you don't-20241121145030701.webp]]

> [!success]
> From the outputs `10.200.X.30` is the only machine with SMB Signing disabled. So this will be the victim !


---
## Answer the questions below

> [!question]
> What host has SMB signing disabled?
> `DC-SRV01`


**Next step:** [[Task 47 - Why not just turn it off?]]
