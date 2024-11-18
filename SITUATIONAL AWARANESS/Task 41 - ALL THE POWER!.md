Now that we understand detections and system surface on the endpoint, we can begin looking at the user and groups of the system. This step of situational awareness can allow us to find privileges and user connections for future horizontal movement or privilege escalation.  

The first tool we will be looking at is PowerView, [https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon). This tool is no longer supported but is still considered a standard for enumeration. From the PowerSploit GitHub, "PowerView is a PowerShell tool to gain network situational awareness on Windows domains. It contains a set of pure-PowerShell replacements for various windows "net *" commands, which utilize PowerShell AD hooks and underlying Win32 API functions to perform useful Windows domain functionality."  

To use the script, we will first need to import it then run the commands that we want to enumerate the endpoint. Find syntax and a few essential commands you can use with PowerView.  

Syntax: `Import-Module .\PowerView.ps1`

We can now run all of the commands that PowerView offers. In this task, we will be focusing on enumerating the local user and group policy surface. In the next task, we will use native PowerShell to enumerate the active directory surface. Outlined below is a list of commands we will cover in this task.

- `Get-NetLocalGroup`
- `Get-NetLocalGroupMember`
- `Get-NetLoggedon`
- `Get-DomainGPO`
- `Find-LocalAdminAccess`

For a complete list of commands, check out the GitHub readme, [https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon#powerview](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon#powerview)  

---

The first PowerView command we will be looking at is `Get-NetLocalGroup`; this command will enumerate/list all groups present on a local machine/computer. Find the syntax and output for the command below.  

Syntax: `Get-NetLocalGroup`

![[Task 41 - ALL THE POWER!-20241118133809052.webp]]

The second PowerView command we will be looking at is `Get-NetLocalGroupMember`; this command will enumerate/list all members of a local group such as users, computers, or service accounts. Find the syntax and output for the command below.

Syntax: `Get-NetLocalGroupMember -Group <group>`

![[Task 41 - ALL THE POWER!-20241118133843273.webp]]

The third PowerView command we will be looking at is `Get-NetLoggedon`; this command will enumerate/list all users currently logged onto the local machine/computer. This can be useful to identify what user's not to take over or what users to target in phishing or other attacks depending on your team's methodology and/or goals. Find the syntax and output for the command below.  

Syntax: `Get-NetLoggedon`

![[Task 41 - ALL THE POWER!-20241118133914257.webp]]

The fourth PowerView command we will be looking at is `Get-DomainGPO`; this command will enumerate/list the active directory domain GPOs installed on the local machine. This can be useful in identifying utilities like AppLocker or other remote services running on the machine/computer. Find the syntax and output for the command below.  

Syntax: `Get-DomainGPO`

![[Task 41 - ALL THE POWER!-20241118133943980.webp]]

The final PowerView command we will be looking at is `Find-LocalAdminAccess`; this command will check all hosts connected to the domain a machine/computer is a part of and check if the current user or listed user is a local administrator. This can be helpful when targeting a specific user and attempting to move across the domain laterally. This can be used as an alternative to other tools like CME for passing the hash. Find the syntax and output for the command below.  

Syntax: `Find-LocalAdminAccess`

![[Task 41 - ALL THE POWER!-20241118134044360.webp]]


For a complete list of commands and cheat-sheets, check out the following resources,  

- [](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)[https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)
- [](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon#powerview)[https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon#powerview](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon#powerview)
- [](https://github.com/HarmJ0y/CheatSheets/blob/master/PowerView.pdf)[https://github.com/HarmJ0y/CheatSheets/blob/master/PowerView.pdf](https://github.com/HarmJ0y/CheatSheets/blob/master/PowerView.pdf)

To run PowerView in Covenant, we can utilize `PowerShellImport` mentioned in Task 25.  

As with most offensive tooling, Defender detects this script. You will need to follow the methodology given in Task 31-36 to execute this tool and evade detections.

---
# Your job

After connecting via RDP to PC-FILESRV01:

- On your attacking machine, download `PowerView.ps1` and copy the file to PC-FILESRV01.
- Open Powershell.
- import the module: `Import-Module .\PowerView.ps1`.
- Find out all domain users.
	`Get-DomainUser`
	 ![[Task 41 - ALL THE POWER!-20241118141025851.webp]]
- List all "Domain Admins" group members.
	 ![[Task 41 - ALL THE POWER!-20241118141308355.webp]]
	You have DA targets.
	- Shirikami Fubuki
    - Inugami Korone

- Findi out logged on users.
	![[Task 41 - ALL THE POWER!-20241118141953890.webp]]

- Further Domain exploration is available, such as is there a trust and what are the GPOs.
	`Get-DomainTrust`
	Returns nothing.

	`Get-DomainGPO`
	![[Task 41 - ALL THE POWER!-20241118142510655.webp]]

- List all groups.
	`Get-NetLocalGroup`
	![[Task 41 - ALL THE POWER!-20241118142736335.webp]]

- Query a specific group.
	`Get-NetLocalGroupMember -Group Administrators`
	
	![[Task 41 - ALL THE POWER!-20241118142946890.webp]]

Once you are confident with the commands, that is enough for this section.

**Next step:** [[Task 42 - Import-Module PowerUpGreySkull.ps1]]
