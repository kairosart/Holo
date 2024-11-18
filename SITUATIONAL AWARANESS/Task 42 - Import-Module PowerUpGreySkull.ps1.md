In some instances, depending on detections and restrictions within the endpoint, you may not run tools like Seatbelt and PowerView. In this case, we can utilize offensive PowerShell commands to perform situational awareness. In addition, Powershell natively supports several modules and commands that we can use to gain situational awareness and enumerate the system/environment.  

We will only be covering a small surface of what PowerShell is capable of. Look below for an outline of commands and modules we will cover in this task.  

- `Get-ScheduledTask`
- `Get-ScheduledTaskInfo`
- `whoami /priv`
- `Get-ADGroup`
- `Get-ADGroupMember`
- `Get-ADPrincipalGroupMembership`

You will notice that most of the modules are focused on active directory structure; this is because the active directory plug-in/modules give us a large amount of control designed for system administrators. The first two commands we will be looking at are aimed towards identifying misconfigurations we can abuse for privilege escalation.

---

## Get-ScheduledTask

The first PowerShell command we will be looking at is `Get-ScheduledTask`; as the command says it will list/enumerate all the scheduled tasks present on the system. To list all tasks, there are no parameters needed to pass to the command. Find syntax for the command below.  

Syntax: `Get-ScheduledTask`

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118143350896.webp]]

You will notice that there is a large number of tasks present; this is because Windows operates at startup with a large number of tasks default on every Windows install. We can use filters and parameters to eliminate some of the unneeded tasks to focus on obscure tasks that we can abuse. Find syntax for filtering below.  

Syntax: `Get-ScheduledTask -TaskPath "\Users\*"`

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118143445389.webp]]

You can experiment with parameters and inputs to get the most optimal output for system enumeration.  

For more information about `Get-ScheduledTask`, check out the Microsoft docs, [https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtask](https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtask)

## Get-ScheduledTaskInfo

The second PowerShell command we will be looking at is `Get-ScheduledTaskInfo`; similar to Get-ScheduledTask, this command will list specific information on specified Tasks allowing the attacker to identify the task and how it could be exploited. Find syntax for the command below.  

Syntax: `Get-ScheduledTaskInfo -TaskName <Full Path>`

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118143652508.webp]]

For more information about Get-ScheduledTaskInfo, check out the Microsoft docs, [https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtaskinfo](https://docs.microsoft.com/en-us/powershell/module/scheduledtasks/get-scheduledtaskinfo)

## whomai /priv

The third command, `whomai /priv`; isn't specific to PowerShell, but can help us with privilege escalation enumeration, as there are many exploits available with misconfigured privileges. The `/priv` parameter will enumerate the _SE privileges_ of the current user. Find the command used and output below.  

Command: `whoami /priv`

![[2Sh2OlE.png]]

## Get-ADGroup

The fourth PowerShell command we will be looking at is `Get-ADGroup`; this module, part of the active directory module package, will allow us to enumerate a user's groups or all groups within the domain. To get the most out of this command, we will already need to enumerate the users present on the machine. Since this command is part of the ActiveDirectory module, you will need first to import the module. Find the syntax for the command below.  

Syntax: `Import-Module ActiveDirectory; Get-ADGroup`

After running the command, you will be prompted with a CLI to apply filters to the command; we recommend filtering by the `samAccountName`. Find example usage for this filter below.  

Syntax: `samAccountName -like "*"`

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118144028591.webp]]

To get the most out of this command, you will need to play with the filters and parameters used to get the most efficient output to enumerate the critical information.  

For more information about `Get-ADGroup`, check out the Microsoft docs, [https://docs.microsoft.com/en-us/powershell/module/addsadministration/get-adgroup](https://docs.microsoft.com/en-us/powershell/module/addsadministration/get-adgroup)

## Get-ADGroupMember

The fifth PowerShell command we will be looking at is `Get-ADGroupMember`; similar to `Get-ADGroup`, this command will list the members of an active directory group. Once you have enumerated groups present on the domain, this command can be helpful to identify specific users that you can target, whether it be for privilege escalation or lateral movement. Since this command is part of the _ActiveDirectory_ module, you will need first to import the module. Find the syntax for the command below.  

Syntax: `Import-Module ActiveDirectory; Get-ADGroupMember`

After running the command, you will be prompted with a CLI to specify the group(s) you want to enumerate. As previously stated, you can get the groups from the previous enumeration with `Get-ADGroup`.

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118144230415.webp]]

For more information about Get-ADGroupMember, check out the Microsoft docs, [https://docs.microsoft.com/en-us/powershell/module/addsadministration/get-adgroupmember](https://docs.microsoft.com/en-us/powershell/module/addsadministration/get-adgroupmember)

## Get-ADPrincipalGroupMembership

The final PowerShell command we will be looking at is `Get-ADPrincipalGroupMembership`; similar to `Get-ADGroupMember`; this command will retrieve the groups a user, computer group, or service account is a member. To get the most out of this command, we will need to have enumerated target users using other commands like `Get-ADUser`. Since this command is part of the _ActiveDirectory_ module, you will need first to import the module. Find the syntax for the command below.  

Syntax: `Import-Module ActiveDirectory; Get-ADPrincipalGroupMembership`

After running the command, you will be prompted with a CLI to specify the user(s) you want to enumerate.

![[Task 42 - Import-Module PowerUpGreySkull.ps1-20241118144538579.webp]]

When using PowerShell for offensive operations, you will need to play around with the commands and modules to see what works for you and develop your methodology similar to working with other tools.

**Next step:**  [[Task 43 - WERE TAKING OVER THIS DLL!]]
