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