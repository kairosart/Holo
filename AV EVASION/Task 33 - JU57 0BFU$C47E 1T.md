Now that we have bypassed AMSI, we need to obfuscate and modify our launcher and grunt code to evade anti-virus. We will begin by understanding the basics of using automated obfuscators like `Invoke-Obfuscation` and `ISE-Steroids` to perform advanced string and signature manipulation.  

We again recommend using a development virtual machine to test and edit code.  

Invoke-Obfuscation, [https://github.com/danielbohannon/Invoke-Obfuscation](https://github.com/danielbohannon/Invoke-Obfuscation), is a utility built by Daniel Bohannon and Cobbr. It is used to take a series of arguments/obfuscation tokens and automatically obfuscate provided code. From their GitHub, *"Invoke-Obfuscation is a PowerShell v2.0+ compatible PowerShell command and script obfuscator."*. Red teamers can use obfuscation to make reverse engineering/analysis harder and, in some cases, bypass anti-virus and other detections.  

Invoke-Obfuscation syntax can seem very large and scary at first if you don't understand how it breaks down the obfuscation tokens. We can follow along with this guide created by the author of Invoke-Obfuscation to get familiar with the syntax [https://www.danielbohannon.com/blog-1/2017/12/2/the-invoke-obfuscation-usage-guide](https://www.danielbohannon.com/blog-1/2017/12/2/the-invoke-obfuscation-usage-guide).  

To begin our obfuscation attempts, we will need to set the script block or the payload we want to obfuscate and then specify tokens to use. Invoke-Obfuscation offers both an argument parsing command-line tool as well as a friendly CLI (Command Line Interface). For our purposes, we will be using the command line. We will only be covering an example of using a token to bypass anti-virus, creating a token command, and the various use cases are out of scope for this task.  

Below is the command we will use to obfuscate our payload. The token command used at the time of writing will bypass anti-virus for some payloads or tools. We will be breaking this command down later in this task.

`Invoke-Obfuscation -ScriptBlock {'Payload Here'} -Command 'Token\\String\\1,2,\\Whitespace\\1' -Quiet -NoExit`

To begin breaking down the command, we will first look at the arguments passed to the tool. The `ScriptBlock` argument will parse your payload or code used to be obfuscated. The two arguments at the end of the command `-Quiet` and `-NoExit` will produce minimal verbosity and prevent exiting from the CLI when the command is run.  

The token used can be found by itself below, along with an explanation of what the token is doing.  

`Token\\String\\1,2,\\Whitespace\\1`

To begin understanding the syntax, we need to understand the tree structure of Invoke-Obfuscation itself. The CLI helps with this and can break down each syntax tree in the overall syntax.

The first initial tree in this syntax is `Token\\String\\1,2,\\` this means it will both concatenate and reorder characters in a string. We can get this information from the CLI syntax tree found below.

![[Task 33 - JU57 0BFU$C47E 1T-20241009141844548.webp]]

We can see both of the types of string obfuscation broken down, and examples are given.  

1. `TOKEN\\STRING\\1` - ('co'+'ffe'+'e')
2. `TOKEN\\STRING\\2` - ('{1}{0}'-f'ffee','co')

The token command will also use a second syntax tree, this time obfuscating using whitespace in `Token\\Whitespace\\1`. We can again get this information from the CLI syntax tree found below.

![[Task 33 - JU57 0BFU$C47E 1T-20241009141950734.webp]]

We can see that the obfuscation technique will randomly add whitespace to the provided strings and payload, along with an example of how it is used.  

1. `TOKEN\\WHITESPACE\\1` - ( 'co' +'fee' + 'e')

When creating a token command, you will need to be careful not to obfuscate the payload too much and exceed the `8191 character limit` in a Windows command prompt. For more information about character limitation look at the Microsoft documentation, [https://docs.microsoft.com/en-us/troubleshoot/windows-client/shell-experience/command-line-string-limitation](https://docs.microsoft.com/en-us/troubleshoot/windows-client/shell-experience/command-line-string-limitation)

If obfuscated efficiently, you should now have a successful PowerShell payload that will bypass anti-virus and make reverse engineering harder. Before executing on a production environment, you should always experiment and test on your development server to ensure that everything goes smoothly during the actual production engagement.  

In the next task, we will cover what you can do when obfuscation fails, or you need to use something that isn't purely written in PowerShell by utilizing code review and ThreatCheck/DefenderCheck.

**Next step:** [[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?']]
