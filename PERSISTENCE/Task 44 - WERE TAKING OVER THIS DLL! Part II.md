Now that we have administrator privileges or moved the application onto our local development machine, we can search for other vulnerable DLL locations using ProcMon or Processhacker2. We can identify vulnerable DLLs by finding the process and PID of the application we are targeting then use filters or modules to search for the DLLs that meet specific requirements to be vulnerable. To be vulnerable, a DLL must meet the below requirements.  

- Defined by the target application
- Ends with .DLL
- Must be run by the target application
- DLL does not exist on the system
- Write privileges for DLL location

In this task, we will be focusing on identifying DLLs with ProcMon, part of the Sysinternals suite [https://docs.microsoft.com/en-us/sysinternals/downloads/procmon](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon).

To begin, we will need to open **ProcMon as an Administrator**. ProcMon will start with an extensive list of all DLLs and processes from all PIDs running on the system. To aid us, we can apply filters to this output to identify information.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134539981.webp]]

To open the filters, navigate to _filter > Filter._  

You will want to filter based on the process name so change the filter to be: `Process Name, Contains, Name of Vulnerable Application`, then navigate to add and add the filter to ProcMon.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134624385.webp]]

If you look at the process list, there will only be processes from the vulnerable application.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134716473.webp]]

Now that we have refined the search down to the application, we can filter it again to only show .DLL files.  

You will filter on the pathname so change the filter to be: `Path, ends with, .dll`, then navigate to add and add the filter to ProcMon.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134815046.webp]]

Now that we have refined our search again, we can look through the output of DLLs run by the vulnerable application.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134852312.webp]]

When looking for a DLL to target, we want to look for a DLL in a path that we can write. We also want to ensure the DLL does not exist on the system in its current state; this will show up as `NAME NOT FOUND`. This means the application attempts to load it but cannot because it does not exist on the system. We can then hijack and use it to run our malicious code.  

Since we're looking for files with paths that we can access, we may want to filter again to something like Desktop, Downloads, Documents. This will allow us to refine our search further for DLLs that we can write. The preferred way to filter again would be to filter the result based on `NAME NOT FOUND`.

You will filter on the result so change the filter to be: Result, contains, `NAME NOT FOUND`, then navigate to add and add the filter to ProcMon.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121134947576.webp]]

If we look at the list of processes in ProcMon, we will see a list of DLLs that can be exploited.

![[Task 44 - WERE TAKING OVER THIS DLL! Part II-20241121135040489.webp]]

We can now control the DLLs using our previously created malicious DLLs and set up quiet persistence on the device by working off applications and processes already running.


---
# Your job

### Identify applications PID

You need Procmon.exe (Process Monitor) to get the name of the vulnerable application. 
*"Process Monitor is an advanced monitoring tool for Windows that shows real-time file system, Registry and process/thread activity."*

- Download it from [here](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon).
- Copy the file to the C:\Windows\Task.
- Run the application.
	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120140053506.webp]]

### Identify vulnerable DLLs that can be hijacked

- Minimize Procmon and start up `kavremover.exe`. press accept and exit when hit with the following screen.

	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120140155634.webp]]

- Close the kavremover executable, as the information till now has already been logged in ProcMon.

> [!note]
> Why do we not proceed further when we can probably find more DLLs? well, when attacking, it is preferred that it requires little to no user interaction in order to complete the attack. That’s the reason we stop here in hopes to achieve the above said goal. If one cannot find any DLLs , they may try to go further ahead to see if anything pops up, but for this post, we will exit here.

- Go baack to Procmon and filter the results.

- Filter: process name contains `kavremover`.

	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120141101049.webp]]

- Filter: Path ends with `.dll`.

	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120141236612.webp]]

> [!note]
> You want to hijack a dll that the application tries to load but isn’t actually present on the system. So whenever the dll is not present on the system, its result is logged as “NAME NOT FOUND” in ProcMon.

- Filter: Result is `NAME NOT FOUND`.

	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120141848772.webp]]
	![[Task 43 - WERE TAKING OVER THIS DLL!-20241120142020794.webp]]

> [!note]
> As you can see, now only entries with NAME NOT FOUND are left. So now in front of us, we have list of DLLs that kavremover couldn’t find on system but wants to load. Press Apply and OK if you haven’t already.Even though we have so many DLLs that the executable couldn’t find on system, we cannot hijack each and every one of them. In the path, look for path that corresponds to the same directory from where the application was loaded ( In this case, its `C:\Users\watamet\Applications\kavremoverENU.dll`).
> This is the dll that we will try to hijack.

> [!Question]
> **Why not other DLL?**
> 
> So you might have a question as to why not just choose a random dll from this?. Well, from the attacker’s perspective, it has locations that they cannot access due to insufficient privileges. We can hijack any of the DLLs in the list, but from an attacker’s perspective, the application is only vulnerable if he can somehow access that path. There are two scenarios, one maybe the user has user privileges with some exploit and is in post exploitation phase. In this case, he can choose any path that the current user has access to. Other one (Common one) is that he is trying to get foothold on the machine by social engineering victim into downloading the malicious dll or something similar. So it’s safe to assume that if your victim downloads your malicious application, then he will run from the same location, and thus any dll in that path is accessible to us. Or you can think of it as providing crack for a game. You only have access to that particular folder where it was downloaded. That’s the reason we look for DLLs that are trying to be loaded from the same directory( desktop in my case ).


---
## Answer the questions below

> [!question]
> What is the first listed vulnerable DLL located in the Windows folder from the application?
> `wow64log.dll`

**Next step:** [[Task 45 - Never trust the LanMan]]
