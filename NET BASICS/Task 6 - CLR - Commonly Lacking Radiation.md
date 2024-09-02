An integral part of working with Windows and other operating system implementations is understanding C# and its underlying technology, .NET. Many Windows applications and utilities are built in C# as it allows developers to interact with the CLR and Win32 API. We will cover the infrastructure behind .NET and its use cases within Windows further below.

![[CLR - Commonly Lacking Radiation-20240902135824796.webp]]

.NET uses a run-time environment known as the Common Language Runtime (CLR). We can use any .NET language (C#, PowerShell, etc.) to compile into the Common Intermediary Language (CIL). NET also interfaces directly with Win32 and API calls making the optimal solution for Windows application development and offensive tool development.

From Microsoft, ".NET provides a run-time environment, called the common language runtime, that runs the code and provides services that make the development process easier. Compilers and tools expose the common language runtime's functionality and enable you to write code that benefits from this managed execution environment. Code that you develop with a language compiler that targets the runtime is called managed code. Managed code benefits from features such as cross-language integration, cross-language exception handling, enhanced security, versioning and deployment support, a simplified model for component interaction, and debugging and profiling services."


---

.NET consists of two different branches with different purposes, outlined below.

- .NET Framework (Windows only)
- .NET Core (Cross-Compatible)

The main component of .NET is .NET assemblies. .NET assemblies are compiled .exes and .dlls that any .NET language can execute.

The CLR will compile the CIL into native machine code. You can find the flow of code within .NET below.

.NET Language → CIL/MSIL → CLR → machine code

You can also decide to use unmanaged code with .NET; code will be directly compiled from the language into machine code, skipping the CLR. Examples of unmanaged code are tools like Donut and UnmanagedPowerShell. Find a visual of data flow within both managed and unmanaged code below.

![[Screenshot from 2024-09-02 20-00-40.png]]

Within .NET, there also exists the Dynamic Language Runtime (DLR). This concept is out of scope for this network; however, to learn more about it, check out this article, [](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamic-language-runtime-overview)[https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamic-language-runtime-overview](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamic-language-runtime-overview)

Now that we have a basic understanding of .NET and how it can interact with the system from .NET languages, we can begin developing and building offensive tooling to aid us in our operations.

**Next etep:** [[Task 7 - Rage Against the Compiler]]