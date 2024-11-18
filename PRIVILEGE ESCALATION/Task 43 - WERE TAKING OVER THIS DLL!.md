Now that we have performed all the enumeration and situational awareness, we can move on to privilege escalation. Looking through our enumeration steps, you may notice a unique application connected to a scheduled task on the endpoint. We can attempt a DLL hijack on this application to escalate privileges, then set up persistence on the endpoint.

From the MITRE ATT&CK framework, DLL Hijacking is defined as "Adversaries may execute their own malicious payloads by hijacking the search order used to load DLLs. Windows systems use a common method to look for required DLLs to load into a program. [[1]](https://docs.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-search-order?redirectedfrom=MSDN) Hijacking DLL loads may be for the purpose of establishing persistence as well as elevating privileges and/or evading restrictions on file execution." The AT&CK Technique ID is [T1574](https://attack.mitre.org/techniques/T1574/).  

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

