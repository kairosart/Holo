An important part of C# and building offensive tooling is understanding how to compile your tools and tools without pre-built releases. To work with C# and building tools, we will again utilize Visual Studio. It is important to note that Visual Studio is not the only C# compiler, and there are several other compilers outlined below.

- Roslyn
- GCC
- MinGW
- LLVM
- TCC
- MSBuild

  
In this task, we will be using **Visual Studio** as it is the easiest to comprehend and work with when developing in C#. Visual Studio also allows us to manage packages and .NET versions without headache when building from a solution file.

To build and develop C# in Visual Studio, we recommend using the Windows development virtual machine, [https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/).

To begin using Visual Studio, you will need a valid Microsoft/Outlook account to sign in and authenticate to Visual Studio. It is a simple and free process to create an account if you do not already have one. For more information, check out the Outlook page, [https://outlook.live.com/owa/](https://outlook.live.com/owa/).


---

We will begin our compiling journey by creating and building a solution file from the code we wrote in the previous task.
  

To create a solution file for .NET Core, navigate to _Create a new project > Console App (.NET Core)_. If you want to open a preexisting solution file/project, navigate to _Open a project or solution_.

From here, you can configure your project's Name, Location, and Solution Name. Find a screenshot of the configuration menu below.

![[Rage Against the Compiler-20240902141702654.webp]]

Once created, Visual Studio will automatically add a starting C# hello world file and maintain the solution file for building. Find a screenshot of the file structure below.

![[Rage Against the Compiler-20240902142020711.webp]]

You will notice that Visual Studio will break down the Dependencies, Classes, and Methods in this file tree which can be helpful when debugging or analyzing code.


---

From here, we should have a working, automatically generated C# hello world file that we can use to test our build process. To build a solution file, navigate to **Build > Build Solution** or hold Ctrl+Shift+B. You can also build from applications themselves rather than project solutions; however, that is out of scope for this network. Once run, the console tab should open or begin outputting information. From here, you can monitor the build process and any errors that may occur. If successful, it will output Build: 1 succeeded and the path to the compiled file. Find a screenshot of the build process below.

![[Rage Against the Compiler-20240902142243153.webp]]

You should now have a successfully compiled file that you can run and use on other systems with corresponding .NET versions!

It is important to note that when building other developer's tools, they will often contain several dependencies and packages. Ensure the machine you are using to build the solution has access to the internet to retrieve the needed packages.

**Next step:** [[Task 8 - NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!]]
