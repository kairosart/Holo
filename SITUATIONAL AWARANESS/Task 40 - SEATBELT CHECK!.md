Now that we understand the system's detection measures and what we can and can't do in our attack surface, we can begin moving on to system enumeration. This type of enumeration can help us identify the endpoint's surface better and potential areas for privilege escalation. To allow us to enumerate the endpoint, we will again be utilizing Seatbelt.  

As previously mentioned, Seatbelt is an enumeration tool that will perform many system checks and provide information on an endpoint. This time we will be using all of the modules that Seatbelt has to offer.  

Find a quick overview of some of the essential modules below.  

- `DotNet` Retrieves .NET version
- `LocalGPOs` Finds local group policies applied to machine and local users
- `LocalGroups` Lists non-empty local groups
- `NetworkShares` Lists exposed network shares
- `PowerShell` Retrieves PowerShell version and security settings
- `Processes` Lists running processes
- `TokenPrivileges` Lists enabled token privileges (SeDebug)
- `CredEnum` Lists current user's saved credentials
- `InterestingFiles` Interesting files matching patterns in user folder
- `ScheduledTasks` Scheduled tasks not authored by Microsoft.

Some of the above tasks will require privileges or a desktop session to run. Using Seatbelt for low privileged awareness uses the basic information you can get to identify the system surface.  

These are not nearly all of the modules that Seatbelt has to offer. For more information about all of the modules Seatbelt offers, check out the GitHub readme, [https://github.com/GhostPack/Seatbelt#command-groups](https://github.com/GhostPack/Seatbelt#command-groups)

Find syntax and an example of output below.

Syntax: `.\Seatbelt.exe all`

![[Task 40 - SEATBELT CHECK!-20241116142600939.webp]]

We can also run Seatbelt from Covenant using the Seatbelt module found below.  

Module: `Seatbelt`

You will notice that Seatbelt produces a large amount of output. It can be helpful to <u>save this output to a file</u> to comb through later. You will have to spend a little bit of time searching through the output to get all the information you need on the endpoint.  

For more information about Seatbelt, check out the Seatbelt GitHub page, [https://github.com/GhostPack/Seatbelt#table-of-contents](https://github.com/GhostPack/Seatbelt#table-of-contents).


---
## Answer the questions below

> [!question]
> What CLR version is installed on PC-FILESRV01?
> `4.0.30319`
> What PowerShell version is installed on PC-FILESRV01?
> `5.1.17763.1`
> What Windows build is PC-FILESRV01 running on?
> `17763.1577`

**Next step:** [[Task 41 - ALL THE POWER!]]
