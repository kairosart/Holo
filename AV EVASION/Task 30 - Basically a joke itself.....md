> [!Note]
 Before moving on with AV evasion please read the entire section's notes and tasks. This section contains multiple methods and techniques that you can mix and match to reach the end goal of evading anti-virus.  

Now that we can upload a file, we notice that our shells are killed or fail at uploading because AV catches them. In the following six tasks, we will be covering the vast topic of AV evasion and how it can be used in conjunction with C2 frameworks like Covenant and offensive tooling. The following tasks compound each other; one task alone will not be enough to evade detections itself. You will need to combine many techniques shown until you have successfully written or created a clean payload/tool. To begin bypassing EDR solutions, we need to understand our first enemy, AMSI.

The `Anti-Malware Scan Interface (AMSI)` is a PowerShell security feature that will allow any applications or services to integrate into antimalware products. AMSI will scan payloads and scripts before execution inside of the runtime. From Microsoft, "The Windows Antimalware Scan Interface (AMSI) is a versatile interface standard that allows your applications and services to integrate with any antimalware product that's present on a machine. AMSI provides enhanced malware protection for your end-users and their data, applications, and workloads."  

For more information about AMSI, check out the Windows docs, [https://docs.microsoft.com/en-us/windows/win32/amsi/](https://docs.microsoft.com/en-us/windows/win32/amsi/)  

Find an example of how data flows inside of Windows security features below.

![[Task 30 - Basically a joke itself....-20241009134720931.webp]]

AMSI will send different response codes based on the results of its scans. Find a list of response codes from AMSI below.  

- AMSI_RESULT_CLEAN = 0
- AMSI_RESULT_NOT_DETECTED = 1
- AMSI_RESULT_BLOCKED_BY_ADMIN_START = 16384
- AMSI_RESULT_BLOCKED_BY_ADMIN_END = 20479
- AMSI_RESULT_DETECTED = 32768

AMSI is fully integrated into the following Windows components.  

- User Account Control, or UAC
- PowerShell
- Windows Script Host (wscript and cscript)
- JavaScript and VBScript
- Office VBA macros

AMSI is instrumented in both System.Management.Automation.dll and within the CLR itself. When inside the CLR, it is assumed that Defender is already being instrumented; this means AMSI will only be called when loaded from memory.  

We can look at what PowerShell security features physically look like and are written using InsecurePowerShell, [https://github.com/PowerShell/PowerShell/compare/master...cobbr:master](https://github.com/PowerShell/PowerShell/compare/master...cobbr:master) maintained by Cobbr. InsecurePowerShell is a GitHub repository of PowerShell with security features removed; this means we can look through the compared commits and identify any security features. AMSI is only instrumented in twelve lines of code under `src/System.Management.Automation/engine/runtime/CompiledScriptBlock.cs`. Find the C# code used to instrument AMSI below.

```
var scriptExtent = scriptBlockAst.Extent;  
 if (AmsiUtils.ScanContent(scriptExtent.Text, scriptExtent.File) == AmsiUtils.AmsiNativeMethods.AMSI_RESULT.AMSI_RESULT_DETECTED)  
 {  
  var parseError = new ParseError(scriptExtent, "ScriptContainedMaliciousContent", ParserStrings.ScriptContainedMaliciousContent);  
  throw new ParseException(new[] { parseError });  
 }  
  
 if (ScriptBlock.CheckSuspiciousContent(scriptBlockAst) != null)  
 {  
  HasSuspiciousContent = true;  
 }
```

Third-parties can also instrument AMSI in their products using the methods outlined below.  

- AMSI Win32 API, [https://docs.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-functions](https://docs.microsoft.com/en-us/windows/win32/amsi/antimalware-scan-interface-functions)
- AMSI COM Interface, [https://docs.microsoft.com/en-us/windows/win32/api/amsi/nn-amsi-iamsistream](https://docs.microsoft.com/en-us/windows/win32/api/amsi/nn-amsi-iamsistream)

For more information about AMSI integration in third-party products, check out this Microsoft article, [https://docs.microsoft.com/en-us/windows/win32/amsi/dev-audience](https://docs.microsoft.com/en-us/windows/win32/amsi/dev-audience)[](https://docs.microsoft.com/en-us/windows/win32/amsi/dev-audience)

In the next task, we will look at how we can utilize PowerShell and C# to bypass AMSI.

**Next step:** [[Task 31 - THEY WONT SEE ME IF I YELL!]]

