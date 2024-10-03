Now that we have a listener in Covenant, we can create a **launcher** to deploy a grunt. Again, this will be helpful later when you get onto a Windows box and need to deploy a grunt quickly.  

From the Covenant GitHub, *"Launchers are used to generate, host, and download binaries, scripts, and one-liners to launch new Grunts."*  

There are ten different launchers to choose from within Covenant, each launcher will have its requirements, and some may not be supported on modern operating systems. Launcher types are outlined below.  

- `Binary` Generates a custom binary to launch grunt, does not rely on a system binary.
- `Shellcode` Converts binary to shellcode using donut, [](https://github.com/TheWover/donut)[https://github.com/TheWover/donut](https://github.com/TheWover/donut)
- `PowerShell` Generates PowerShell code to launch a grunt using `powershell.exe`.
- `MSBuild` Generates an MSBuild XML file to launch a grunt using `msbuild.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Msbuild/)[https://lolbas-project.github.io/lolbas/Binaries/Msbuild/](https://lolbas-project.github.io/lolbas/Binaries/Msbuild/)
- `InstallUtil` Generates an InstallUtil XML file to launch a grunt using `installutil.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Installutil/)[https://lolbas-project.github.io/lolbas/Binaries/Installutil/](https://lolbas-project.github.io/lolbas/Binaries/Installutil/)
- `Mshta` Generates an HTA file to launch a grunt using `mshta.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Mshta/)[https://lolbas-project.github.io/lolbas/Binaries/Mshta/](https://lolbas-project.github.io/lolbas/Binaries/Mshta/)
- `Regsrv32` Generates an SCT file to launch a grunt using `regsrv32.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Regsvr32/)[https://lolbas-project.github.io/lolbas/Binaries/Regsvr32/](https://lolbas-project.github.io/lolbas/Binaries/Regsvr32/)
- `Wmic` Generates an XSL file to launch a grunt using `wmic.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Wmic/)[https://lolbas-project.github.io/lolbas/Binaries/Wmic/](https://lolbas-project.github.io/lolbas/Binaries/Wmic/)
- `Cscript` Generate a JScript file to launch a grunt using `cscript.exe`, [](https://lolbas-project.github.io/lolbas/Binaries/Cscript/)[https://lolbas-project.github.io/lolbas/Binaries/Cscript/](https://lolbas-project.github.io/lolbas/Binaries/Cscript/)
- `Wscript` Generate a JScript file to launch a grunt using `wscript.exe`, [https://lolbas-project.github.io/lolbas/Binaries/Wscript/](https://lolbas-project.github.io/lolbas/Binaries/Wscript/)[](https://lolbas-project.github.io/lolbas/Binaries/Wscript/)

There are several options for each launcher, with some launchers having specific options. For this task, we will be focusing on the `Binary` launcher and its options. The configuration options are outlined below.  

- `Listener` Listener the grunt will communicate with.
- `ImplantTemplate` Type of implant launcher will use.
- `DotNetVersion` .NET version launcher will use, dependent on `ImplantTemplate`.
- `Delay` Time grunt will sleep in-between callbacks. A larger delay can aid in stealthy communications.
- `JitterPercent` Percent of variability in `Delay`.
- `ConnectAttempts` Amount of times grunt will attempt to connect back to the server before quitting.
- `KillDate` Date specified grunt will quit and stop calling back.

To create a basic launcher for this network, we only suggest editing the `Listener` and `ImplantTemplate`  .

Once created, the launcher will be downloaded or output a one-liner that can be copied. You can then use the launcher as needed to deploy grunts.

![[Task 26 - The Blood Oath-20241002143153983.webp]]

To deploy a grunt, you will only need to transfer your launcher to your target machine and execute the payload using your preferred method; this will change based on what launcher you decide to use.  

Once executed, the grunt should check back into the server and appear within the _Grunt_ tab.  

**Note:** This is only an example of executing a grunt; you will not need to execute a grunt until later tasks.

![[Task 26 - The Blood Oath-20241002143301067.webp]]

If you navigate to the grunt to interact with it, you will be given an interaction menu. From here, you can remotely control the grunt and execute shell commands and modules. This will be covered further in-depth in *Task 29*.

![[Task 26 - The Blood Oath-20241002143413060.webp]]



---

# Your job

Now you have a listener, you need some code to execute and connect back to it. This is where a launcher comes in.

You have to start a binary launcher, this allows you to use your own binary to launch the grunt, rather than relying on system tools.
- Create a binary launcher.
	- Click on Launcher on the left-side bar.
	- Select `Binary` and  check the listener is correct and as your listener is HTTP you will leave the implant as a `GruntHTTP`..
		![[Task 26 - The Blood Oath-20241002144715707.webp]]
	- The other options are specific for the launcher, things like jitter and delay are used to attempt to avoid AV by being variable in their call backs, and allowing you to “sleep” the c2 beacon. For this you don’t need to change any of those.
	Click on the `Generate` button.This creates an `.exe` called `GruntHTTP`.
	- Click download and you now have a malicious C2 payload ready to let fly.

As it’s an .exe, you need to run it on a Windows machine, which you don’t have access too yet. So, save that for later.

**Next step: ** [[Task 27 - We ran out of Halo and YAML references...]]
