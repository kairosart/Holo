A large part of operating with Covenant is task usage. Covenant, by default, does not come with a large number of tasks/modules to choose from like other C2 frameworks like Empire and PoshC2. This means that we will need to create our own tasks of tools that we want to use within Covenant. Luckily for us, Covenant is built off .NET and C#, making it easy to convert any C# code into a task.  

For this task, we will be converting SharpEDRChecker into a Covenant task; this will later be used in Task 36.  

Since Covenant v0.5, the way that the Covenant backend intakes and parses tasks has changed. Covenant now utilizes YAML files to define tasks and task data. From the YAML website, "YAML is a human-friendly data serialization standard for all programming languages." This makes it easy for developers and operators to weaponize and integrate tooling into Covenant.  

Find an outline below of rules you need to have in mind when building tasks to ensure that your task integrates with the grunt.  

- Define a class called `Task`
- Define a method called `Execute`
- Return a string

We will begin by using an example template that we can later modify and add references to. Find an example YAML template for Covenant below.  

```yaml
- Name: Example
  Aliases: []
  Description: Example task for Covenant
		Name: Tryhackme 
		Handle: THM_User
		Link: <https://twitter.com/RealTryHackMe>
  Help: 
  Language: CSharp
  CompatibleDotNetVersions:
  - Net35
  - Net40
  Code: |
	public static class Task {
	    public static string Execute() {
	        return "Hello Covenant!";
	    }
	} 
```

The above is a basic template that we can use to get the basic structure of our task down. Find an explanation of each YAML tag below.  

- `Name` Name of the task in Covenant UI.
- `Aliases` Aliases or shortcuts for the task.
- `Description` Description of the task in Covenant UI.
- `Language` Language the task source code is written in.
- `CompatibleDotNetVersions` Versions of .NET the source code will run on.
- `Code` Source code of task.

We have a basic structure for our task data, but our task will still not work. Covenant uses .NET; we need to define our reference assemblies that .NET will use to interpret our code and provide basic functionality. Find an example YAML template for reference assemblies below.  

```yaml
 ReferenceAssemblies:
    - Name: mscorlib.dll
      Location: net35\\mscorlib.dll
      DotNetVersion: Net35
    - Name: System.dll
      Location: net35\\System.dll
      DotNetVersion: Net35
    - Name: System.Core.dll
      Location: net35\\System.Core.dll
      DotNetVersion: Net35
	    - Name: mscorlib.dll
      Location: net40/mscorlib.dll
      DotNetVersion: Net40
    - Name: System.dll
      Location: net40/System.dll
      DotNetVersion: Net40
    - Name: System.Core.dll
      Location: net40/System.Core.dll
      DotNetVersion: Net40 
```

Depending on what project we are working on and what assemblies it uses will depend on how many and what reference assemblies we add to this template. For our example task, we will only need to add basic assemblies found in the template above.  

This method of adding reference assemblies can also be used to add reference sources; this is how we can add external C# code. We will be covering this in more depth later in this task.  

We can add together the above YAML to create a final example template that we can use to test our task source code. Find the YAML template below.  

```yaml
- Name: Example
  Aliases: []
  Description: Example task for Covenant
      Name: Tryhackme 
      Handle: THM_User
      Link: <https://twitter.com/RealTryHackMe>
  Help: 
  Language: CSharp
  CompatibleDotNetVersions:
  - Net35
  - Net40
  Code: |
	public static class Task {
	    public static string Execute() {
	        return "Hello Covenant!";
	    }
	}
TaskingType: Assembly
UnsafeCompile: false
TokenTask: false
Options: []
ReferenceAssemblies:
	- Name: mscorlib.dll
	Location: net35\\mscorlib.dll
	DotNetVersion: Net35
	- Name: System.dll
	Location: net35\\System.dll
	DotNetVersion: Net35
	- Name: System.Core.dll
	Location: net35\\System.Core.dll
	DotNetVersion: Net35
	- Name: mscorlib.dll
	Location: net40/mscorlib.dll
	DotNetVersion: Net40
	- Name: System.dll
	Location: net40/System.dll
	DotNetVersion: Net40
	- Name: System.Core.dll
	Location: net40/System.Core.dll
	DotNetVersion: Net40
EmbeddedResources: []
ReferenceAssemblies: []
EmbeddedResources: [] 
```

You can add this YAML file under `Covenant/Covenant/Data/Tasks/`. If we rebuild and run Covenant, our newly created task should appear within the UI and can be used with any grunts now.  

Now that we have a basic task working, we can attempt to convert SharpEDRChecker to Covenant. This process is not as hard as it seems and is fully outlined below.  

First, we will want to place the entire SharpEDRChecker source code repository in `Covenant/Covenant/Data/ReferenceSourceLibraries/`. This will allow Covenants backend to integrate and parse the source code and references of the tool.  

You can also import PowerShell scripts or commands using the PowerShell and PowerShellImport tasks along with creating your own tasks.