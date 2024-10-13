Up to this point, we should have a working PoC payload and grunt. However, in many cases, the basic steps of bypassing AMSI and obfuscating code may not work. In this case, we will need to use other tools and techniques to manually identify bad bytes within the code and review the code to break signatures to get the code past AMSI and Defender cleanly.  

As new EDR solutions and prevention methods are released, we as red teamers need to change and evolve our TTPs to work around the ever-growing blue team. Often, techniques themselves don't change, but scripts and solutions like [https://github.com/IonizeCbr/AmsiPatchDetection](https://github.com/IonizeCbr/AmsiPatchDetection) and indicators can make it harder to get our payloads and tools past even when bypassed and obfuscated, or we have other restrictions in place we need to workaround. In this case, we can use code analysis and manual code review to break signatures. A few tools can help us along the way for code analysis, including ThreatCheck, [https://github.com/rasta-mouse/ThreatCheck](https://github.com/rasta-mouse/ThreatCheck), and DefenderCheck, [https://github.com/matterpreter/DefenderCheck](https://github.com/matterpreter/DefenderCheck). Both of these tools will ingest a given file and output the found bytes attached to signatures.  

## ThreatCheck

We again recommend using a development virtual machine to test and edit code.

### Downloading ThreatCheck compiled

https://github.com/Laransec/ThreatCheck-Compiled

As covered in [[Task 7 - Rage Against the Compiler]], you will need to build Threat Check using a Visual Studio solution file. It is important to note that Threat Check uses multiple NuGet packages; ensure your development machine has internet access to retrieve these packages. The build process will produce an application file, a DLL file, and an XML file. You will need all three files in the same directory for ThreatCheck to work. 

Files will be built to `ThreatCheck-master\\ThreatCheck\\ThreatCheck\\bin\\Debug`.

![[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?'-20241009142517399.webp]]

ThreatCheck has a small argument list, and syntax is relatively straightforward. Find a list of arguments and a syntax example below.  

- `-e` or `—engine` (AMSI or Defender)
- `-f` or `—file`
- `-u` or `—url`

Syntax: `ThreatCheck.exe -f <file>`

In this task, we will be focusing on analyzing the Covenant source code; however, ThreatCheck can be used on any tools or payloads you need to clean.  

Below you will find an example of the first bad byte that ThreatCheck will discover. ThreatCheck will aggregate bytes based on their signature strength, the lowest being the strongest signature and what you should prioritize breaking.

![[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?'-20241009142627311.webp]]

To aid us in breaking up the Covenant signature, we will follow this guide written by RastaMouse, [https://offensivedefence.co.uk/posts/covenant-profiles-templates/](https://offensivedefence.co.uk/posts/covenant-profiles-templates/).

Looking through the output of ThreatCheck, we notice a `WebProxy` along with an `http://192.168.227.139:80`. We can assume it is attached to the listener from these signatures rather than the grunt code itself. To break this signature, we can create a custom listener profile or edit the current HTTP profile.

Thanks to prior research from RastaMouse, we know that you will need to add an HTTP response header to break the signature. If you were going into this blind, you would need to experiment with settings and code to identify where the engine is attaching and what you can do to break it. Add the below line to your listener profile under `Listeners > Profiles > CustomHttpProfile`.

![[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?'-20241009142736433.webp]]

Once added, we can build our agent again and test against ThreatCheck again.

![[Task 34 - 'Ca' + 'n' + 'you' + ' ' + 'see' + 'me now' + '?'-20241009142813928.webp]]

The output above has two signatures attached. Use your knowledge of HTTP requests and responses to break the signature.  

You will also notice a `GUID Type` signature. Use your knowledge of C# from Task 6 along with RastaMouse's guide to break this signature and create a clean grunt.

You will have to repeat this process of going back and forth between ThreatCheck and the source code until you have a clean agent that evades detections.  

If successful, you will now have a clean tool or payload that evades Defender.


---

# Your job

