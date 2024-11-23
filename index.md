![[Screenshot from 2024-09-01 11-47-45.png]]

Welcome to Holo!

Holo is an Active Directory and Web Application attack lab that teaches core web attack vectors and advanced\obscure Active Directory attacks along with general red teaming methodology and concepts.

In this lab, you will learn and explore the following topics:

- .NET basics
- Web application exploitation
- AV evasion
- Whitelist and container escapes
- Pivoting
- Operating with a C2 (Command and Control) Framework
- Post-Exploitation
- Situational Awareness
- Active Directory attacks

You will learn and exploit the following attacks and misconfigurations:

- Misconfigured sub-domains
- Local file Inclusion
- Remote code execution
- Docker containers
- SUID binaries
- Password resets
- Client-side filters
- AppLocker
- Vulnerable DLLs
- Net-NTLMv2 / SMB

This network simulates an external penetration test on a corporate network "Hololive" with one intended kill chain. All concepts and exploits will be taught in a red teaming methodology and mindset with other methods and techniques taught throughout the network.

---

This network brings you from zero to red-team, but you are expected to have a general understanding of basic Windows and Linux architecture and the command line for both Windows and Linux. If you need help, please feel free to ask in the TryHackMe Discord; there is a channel set up for this purpose in the help section there.

---
## **Overview**

Holo is a TryHackMe room that focuses on Active Directory and Web Application exploitation. The room is designed for advanced users and requires stealthy and creative approaches to compromise the Domain Controller.

**Enumeration**

1. **Initial Recon**: The room presents a virtual host routing setup, with multiple subdomains (e.g., www.holo.live, admin.holo.live, dev.holo.live). Use tools like ffuf, wfuzz, or gobuster to identify potential subdomains and vhosts.
2. **Subdomain Bruteforce**: Run a subdomain bruteforce using ffuf or wfuzz against the main domain (holo.live) with a wordlist. Filter results to exclude false positives.
3. **Host File Update**: Add discovered subdomains to the hosts file and visit each one to gather information.

**Web Application Exploitation**

1. **holo.live**: The main website loads a WordPress page, but with image rendering issues. This may indicate a misconfigured web server.
2. **admin.holo.live**: A login page is present, likely an admin dashboard. Attempt to login using default credentials or brute-force.

**Active Directory Exploitation**

1. **Domain Controller Compromise**: The goal is to hack into the Domain Controller as stealthily as possible.
2. **RCE (Remote Code Execution)**: Use discovered vulnerabilities or exploits to achieve RCE on the Domain Controller.

**Additional Tips**

1. **Use Metasploit**: Set up a listener on the tun0 interface and port 53 to catch reverse shells.
2. **One-Liner Solution**: The provided one-liner solution is a more efficient and logless way to achieve RCE.
3. **AMSI (Windows Antimalware Scan Interface)**: Understand AMSI and its role in enhancing malware protection.
4. **Net-NTLMv1 and v2**: Familiarize yourself with these hash protocols and their differences.

## **Conclusion**

The Holo TryHackMe room presents a comprehensive challenge, requiring a combination of web application and Active Directory exploitation skills. By following the enumeration and exploitation steps outlined above, you can successfully compromise the Domain Controller and achieve RCE.


---
## MAIN STEPS

[[Task 14 - Meterpreter session 1 closed. Reason RUH ROH|1. Reverse Shell]]
[[Task 18 - Making Thin Lizzy Proud#Inject our PHP code into a table and save the table into a file on the remote system|2. Inject PHP code into a table]]
[[Task 18 - Making Thin Lizzy Proud#Accessing 192.168.100.1 machine|2.1 Accessing to 192.168.100.1]]
[[Task 19 - Going%20out%20with%20a%20SHEBANG%21#Your job|3. Shell onto  L-SRV01.]]
[[Task 20 - Call me Mario, because I got all the bits#Your job|4. Privilege Escalation L-SRV01]]
[[Task 21 - From the Shadows#Adding an SSH key|5. Adding a SSH Key]]
[[Task 23 - Digging a tunnel to nowhere#Pivoting with sshuttle|6. Pivoting with sshuttle]]
[[Task 28 - Hide yo' Kids, Hide yo' Wives, Hide yo' Tokens#Your job|7. Accesing S-SRV01 Web]]
[[Task 35 - Wrapping the burrito#Uploading a web-shell to 10.200.X.31|8. Uploading a web-sell to 10.200.X.31]]
[[Task 36 -  That's not a cat that's a dawg#Running hoaxshell|9. Hoaxhell and Mimikatz]]
[[Task 37 - Good Intentions, Courtesy of Microsoft - Part II#Connect to PC-FILESRV01 with smbclient|10. Connect to PC-FILESERV01]]
[[Task 38 - Watson left her locker open#applocker-bypas-checker.ps1|11. Applocker bypas]]
[[Task 39 - So it's just fancy malware?#AV enumeration with Seatbelt|12. AV Enumeration]]
[[Task 40 - SEATBELT CHECK!|13. Seatbelt check]]
[[Task 43 - WERE TAKING OVER THIS DLL!#Your job|14. Privelege Escalation to PC-FILESRV01]]
[[Task 44 - WERE TAKING OVER THIS DLL! Part II#Your job|15. Identifying DLL for hijacking]]
[[Task 46 - Now you see me, now you don't#Your job|16. Abuse NTLM relaying]]
[[Task 47 - Why not just turn it off?#Your job|17. Add a SOCKS connection]]
[[Task 48 - Ready your weapons#Your job|18. Gain access to the domain]]


